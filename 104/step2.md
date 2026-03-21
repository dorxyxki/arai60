# 他の方の解法やディスコのログを見て解く

## 別の解法
- 再帰で行きかけに深さを記録してつたえていく．
    - 帰りがけの際はただ部下から帰ってきた値を上に返すだけ

- stack + loop で 再帰で完結に書けていた 帰りがけの処理を書く
    - https://discord.com/channels/1084280443945353267/1201211204547383386/1228400327800127648
    - https://discord.com/channels/1084280443945353267/1227073733844406343/1236695050902048899
    - https://discord.com/channels/1084280443945353267/1227073733844406343/1236682759649497099
    - https://discord.com/channels/1084280443945353267/1227073733844406343/1236324993839792149

## 参考
- DFS と BFS の比較
    - どんな木の形のときに 空間計算量 が最悪になるのか最初から考慮されていた．
        - https://github.com/n6o/leetcode_arai60/pull/20/changes#diff-bf5b108c31cb540b6da824aeb6d185f5457343f41c8e2962e7fdf62e0d13e755R14

- 確認せずに stack, queue に放り込んで 取り出した時に確認する手法をとれば，いちいち右左に子供がいるかの確認をしなくて済む．
    - 過去みた概念にも関わらず，初見でこういう工夫によるコードの改善がパッとできない．
    - https://github.com/plushn/SWE-Arai60/pull/21/changes#r2597534088
        > Noneチェックですが、append前にやる方法のほか、pop直後にやる方法もあります。

        > pop直後にやる方法について、prosはNoneチェックを現状のコードで3箇所あるのを1箇所に集約できる点、consは一時的にNoneがキューに入ることでループが多く回る点です。BFSにて幾何級数的に増加することがあるそうです。

- 番兵で最初の分岐をへらす
    - https://github.com/tom4649/Coding/pull/19/changes#diff-c8043faf79e11f79a18fa3c8dec4ed4a284f068c666d4c1c9d9f7901dd33ae56R19

- helper 関数の命名
    - https://github.com/TakayaShirai/leetcode_practice/pull/21/changes#r2808001206
        > ここで定義されている時点で、スコープ的に maxDepth のヘルパーであることはほぼ自明なので、関数名から取り除いても構わないと思いました。
    - 確かに

- BFS で queue を使わない方法について
    - https://discord.com/channels/1084280443945353267/1201211204547383386/1219179255615717399
        > つまり、一つの変数に、2つの違う種類のものを入れておいて、その境界を個数で管理しているわけですよね。
    - なるほど，frotier に depth = n , depth = n + 1 のノードを入れる（2種類）．その境界を個数でかんりする方法もあるのか．
    - https://github.com/plushn/SWE-Arai60/pull/21/changes#r2597502705


## 2-1
- DFS スタック (1-1 の書き直し)

```py
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        frontier_and_depth = [(root, 1)]
        max_depth = 1
        while frontier_and_depth:
            node, depth = frontier_and_depth.pop()
            if node is None:
                continue
            max_depth = max(max_depth, depth)
            frontier_and_depth.append((node.left, depth + 1))
            frontier_and_depth.append((node.right, depth + 1))
        return max_depth
```

## 2-2
- 再帰DFS（行きがけ）

```py
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        max_depth = 0

        def helper(root: Optional[TreeNode], depth: int) -> int:
            nonlocal max_depth
            if root is None:
                return
            max_depth = max(max_depth, depth)
            helper(root.left, depth + 1)
            helper(root.right, depth + 1)

        helper(root, 1)
        return max_depth
```

## 2-3 
- DFS スタック (帰りがけ)
- ref:
    - https://discord.com/channels/1084280443945353267/1227073733844406343/1236695050902048899
    - https://discord.com/channels/1084280443945353267/1227073733844406343/1236682759649497099
    - https://discord.com/channels/1084280443945353267/1227073733844406343/1236324993839792149
        > 確かにleft_depthやright_depthが無ければ行きがけであることはわかるので'go_back_flag'は無くても大丈夫ですね。
    - フラグ変数を使わなくても，帰るときには必ず left_ret, right_ret の参照ができるので，分岐を減らせるのか

- 難しい，ポインタの概念があまりうまく理解できていないのかも．

```py
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        depth_ret = [None]
        call_stack = [("enter", (root, depth_ret, [None], [None]))]
        while call_stack:
            state, args = call_stack.pop()
            if state == "exit":
                _, parent_depth, left_depth, right_depth = args
                parent_depth[0] = max(left_depth[0], right_depth[0]) + 1
            
            if state == "enter":
                node, parent_depth, _, _ = args
                if node is None:
                    parent_depth[0] = 0
                    continue
                
                left_ret = [None]
                right_ret = [None]
                call_stack.append(("exit", (node, parent_depth, left_ret, right_ret)))
                call_stack.append(("enter", (node.left, left_ret, [None], [None])))
                call_stack.append(("enter", (node.right, right_ret, [None], [None])))

        return depth_ret[0]
```
