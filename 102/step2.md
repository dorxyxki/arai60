# 他の方の解法やディスコのログを見て解く

## 別の解法
- dfs で探索しつつ、level を明示的に扱って level ごとに値を記録していく
    - https://github.com/h-masder/Arai60/pull/29/changes#diff-ab0b43045c3d04383283b0d5f71467941dc12e966bc320f8c34a150788a55ae1R78
    - https://github.com/rimokem/arai60/pull/26/changes#diff-fe1626270141c68eeea2ed66246879468d9cbdf106283b979f822d49ecf83e7dR28
    - 

## 参考
- while か if か
    - https://discord.com/channels/1084280443945353267/1200089668901937312/1211248049884499988
        > 「level が大きくて nodes_ordered_by_level が足りない場合、足りるように拡張します。そして、拡張した場所に書き込みます。」(読んでいくと、あとから、足りないことがあったとしても1段であることが他のところから分かる。)
        > 「level が大きくて nodes_ordered_by_level が足りない場合、1段だけ拡張します。そして、level 番目に書き込みます。(書き込めなかったら IndexError が投げられます。)」(読んでいくと、1段だけしか拡張しなくても、level 番目が準備されているので例外はないことが分かる。)
        >というふうに読めます。どっちが読み手にとっていいですか。
    
    - コードを書く時に読み手にパズルを解かせないようにする。ロジックから自明でしょみたいなのは避けるのが大事か

- filter 関数を使って書く方法
    - https://github.com/Satorien/LeetCode/pull/26/changes#diff-90927eee773b5b7463148deda09c828069faa6ab54b7559cb40bcdf0849cc795R76
        > `nodes = list(filter(None, nodes))`
    - https://docs.python.org/3/library/functions.html#filter
        > Note that filter(function, iterable) is equivalent to the generator expression (item for item in iterable if function(item)) if function is not None and (item for item in iterable if item) if function is None.
    
    - 第一引数に None を渡すというのがあまり直感的ではないと感じた。

- node のNoneチェックをいつやるか
    - https://github.com/naoto-iwase/leetcode/pull/30/changes#diff-40c0807c5c71abb50c5c8ebe78ffc21551f9ab6d80ccd6a156c960d1febd1882R60
        > Noneチェックをキューから取り出すときにやる実装も書いてみる。
        > - メリットはNoneチェックを1箇所で書けることや、root is Noneを特別扱いしなくて良い。
        > - デメリットとしては、whileループが常に(深さ + 1)回ることや、それに伴う後処理が直感的でない（一種のパズルになっている）こと。
    
    - ノード数が多いとwhileループが常に(深さ + 1)回るデメリットが定数倍で無視できなくなる場合もありそう

## 2-1
- 1-1 のリファクタ
    - frontier に入れる前に None チェックをする

```py
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []

        values_by_level = []
        frontier = [root]

        while frontier:
            next_frontier = []
            values = []

            for node in frontier:
                values.append(node.val)
                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)
            
            values_by_level.append(values)
            frontier = next_frontier
        
        return values_by_level
```

## 2-2
- 練習
    - dfs で level ごとにノードの値を入れていく方法
    - 右から入れていかないと level ごとにおいて左から右に並ばない

```py
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        node_and_levels = [(root, 1)]
        vals_by_level = []

        while node_and_levels:
            node, level = node_and_levels.pop()
            if node is None:
                continue
            
            while len(vals_by_level) < level:
                vals_by_level.append([])

            vals_by_level[level - 1].append(node.val)
            node_and_levels.append((node.right, level + 1))
            node_and_levels.append((node.left, level + 1))
        
        return vals_by_level
```
