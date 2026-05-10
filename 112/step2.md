# 他の方の解法やディスコのログを見て解く

## 別の解法
- target_sum から引いていく手法
    - https://github.com/Manato110/LeetCode-arai60/pull/25/changes#diff-6ed53d0a1e8d2f64985c06a8e270bd25d1e1fcfd46487a8a3ebd4e74cea9691bR129
    - https://github.com/rimokem/arai60/pull/25/changes#diff-27fc9dd71685bbf3dde14a896069f62d5f3c3f231514f4c8689bffa556251f8fR48

## 参考
- stack において 右の子から追加してあげることについて
    - https://github.com/kitano-kazuki/leetcode/pull/25/changes#r2997330615
        > 木探索は左の子からの探索が一般的ですが、stackを利用しているため、この順序を守りたい場合にはnode.rightを先にappendするのが良いと思いました

- 変数名について
    - https://github.com/rimokem/arai60/pull/25/changes#r3141785869
    - https://github.com/h-masder/Arai60/pull/28/changes#r3143170907
        - 中に何が入っているのかという視点で `node_and_total` 等が良さそう
    - https://github.com/kitano-kazuki/leetcode/pull/25/changes#r3146195747
        > 要素数が多くなってきたら、 dataclass 化などを行い、 1 つの要素にし、クラス名を変数名に含めてしまうのが良いと思います。

- フォローアップ質問
    - https://github.com/naoto-iwase/leetcode/pull/29#discussion_r2455081026
        > もし葉でtargetSumになるような経路を返却するとしたらどうでしょうか？        
        - 探索の時に自分の親は誰かを記録する辞書を作って、後々葉から再構築すれば良さそう。そうすると空間計算量 O(N) になる
        - path そのものを引き継いでいく場合
            - BFS だと空間計算量 O(N x h) に
            - DFS だと path を引き継いでいけば空間計算量 O(h) に抑えられるか

## 2-1
- 1-1 のリファクタ

```py
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False

        node_and_total = [(root, 0)]
        while node_and_total:
            node, path_sum = node_and_total.pop()
            path_sum += node.val
            
            if node.left is None and node.right is None:
                if path_sum == targetSum:
                    return True
                continue

            if node.right is not None:
                node_and_total.append((node.right, path_sum))
            if node.left is not None:
                node_and_total.append((node.left, path_sum))
        
        return False
```

## 2-2

```py
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False

        targetSum -= root.val
        if root.left is None and root.right is None:
            if targetSum == 0:
                return True
        
        return self.hasPathSum(root.left, targetSum) or self.hasPathSum(root.right, targetSum)
```
