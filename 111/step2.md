# 他の方の解法やディスコのログを見て解く


## 他の解放
- stack + loop でしたから上げていくように書く
    - https://github.com/tom4649/Coding/pull/20/changes#diff-a8d0fa165558b705bb36488c15d529529adde82f828ad419e84cde32ab1097b7R11
        - dict で node ごとに 深さを登録している．完結で分かりやすい
    - https://github.com/tokuhirat/LeetCode/pull/22/changes#diff-5a9b386008e4a60687b34d8215010a933820dff897115dbd8a20a07a008b097aR118
        - namedtuple, dataclass, typeddict などをつかうとみやすく
    - https://github.com/potrue/leetcode/pull/22/changes/BASE..432bfc55f628fee00926521b245797d872ff4501#r2112567800
        - 行きと帰りのフラグ変数をなくしてもかける．
        - 子供がいるのにその子供の深さが記録されてなければ行き，（両方とも子供がいなければ深さを記録）
        - それ以外は帰りで深さをアップデート

## 参考
- 左右に対する探索をリスト or タプルで表現
    - https://github.com/mamo3gr/arai60/pull/20/changes#diff-9eb4930f4da2d91034d920481ed8541eadf84051f4bf6bfec14f60f742f90ae4R56
    - https://github.com/tom4649/Coding/pull/20/changes#diff-df4fcb20269fccc17828812fd9b2713d8191d2bd029c16e01a5c6bf1759f5edfR14
    - 拡張性高そうでいい

- unreachable な部分について
    - 特に，1-1 で while を抜けた後に何も書かれていない部分
        - https://github.com/kitano-kazuki/leetcode/pull/22/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R119
        - https://github.com/mamo3gr/arai60/pull/20/changes#diff-7baccfee0698627630074e09b6e132a8f0443e75aa7b60fd91ac3a1ee605e4f4R31
            - なるほど，`RuntimeError` を出すのも一つの選択肢

        - https://github.com/h1rosaka/arai60/pull/25#discussion_r2269399945
            > unreachable であることを明示的に伝えるように、何かを入れたほうが良いと思います。
        - `raise Exception("Unreachable")` のような形か

        - https://github.com/TORUS0818/leetcode/pull/44#discussion_r1996702695
            > Unreachable なところに raise を書くことですが、ありかもしれませんが、私はそこまで肯定的ではないです。結構微妙なところだと思います。

            > まず、一般的に、dead code は避けるものです。
            > また、Python の場合、返り値があって到達する場合はあってもなくても同じだが return None を書き、到達しない場合は書かないことで、unreachable かの意図は表現されるはずです。
            > それでは弱く、よほど気になるならば、コメントを一つ書いておくくらいが適切かもしれません。
        - 色々な意見がある．今回だと，コメントを書くのもいいのか

        - https://github.com/fuga-98/arai60/pull/22#discussion_r1996619544
            > 受け取らなくて良く、後に編集する開発者に制約を明示するという目的ならば、assert を入れる、というのは一つあります。assert はデバッグ時のみ走る条件で、実態は、条件と例外です。

        - assert という選択肢もあるか   
            - https://github.com/Yuto729/LeetCode_arai60/pull/27/changes#diff-03d92449381f3da5f29c49baadfaca72e4cc34da688da26d79f7a1d746fccac1R55


- 作業の分離について
    - https://github.com/mamo3gr/arai60/pull/20/changes#r2670757871
        > 入力がNoneでないことをminDepth()本体で確認してから，引数にNoneでないTreeNodeをとる再帰用のヘルパー関数（12行目以下の内容をそのまま関数化）を用意してそちらにrootを渡す方が，作業の分離という意味でわかりやすいと感じます．
    - 再帰用のヘルパーを作って，分岐をするのもあり

- depth を増やすタイミングについて
    - https://github.com/olsen-blue/Arai60/pull/22#discussion_r1925335296
        > この depth の更新は while の一番下のほうが素直じゃないでしょうか。(つまり、nodes_depth の更新とともに数を増やします。)
    
## 2-1
- BFS

```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        def is_leaf(root: TreeNode) -> bool:
            return node.left is None and node.right is None

        frontier = (root]
        depth = 1

        while frontier:
            next_frontier = []

            for node in frontier:                
                if is_leaf(node):
                    return depth

                for child in (node.left, node.right):
                    if child is None:
                        continue
                    next_frontier.append(child)

            frontier = next_frontier
            depth += 1

        assert False, "unreachable"
```


## 2-2
- 再帰でしたから記録していく

```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        def helper(root: TreeNode) -> int:
            if root.left is None and root.right is None:
                return 1
            
            child_depth = []
            for child in (root.left, root.right):
                if child is None:
                    continue
                child_depth.append(helper(child))

            return min(child_depth) + 1

        return helper(root)
```

## 2-3
- stack + loop でしたから記録していく形
    - 練習として

```py
from enum import Enum, auto

class State(Enum):
    EXIT = auto()
    ENTER = auto()

class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        root_depth = [None]
        stack_frame = [(State.ENTER, (root, root_depth, [None], [None]))]
        while stack_frame:
            state, args = stack_frame.pop()

            if state == State.ENTER:
                node, parent_depth, _, _ = args
                if node.right is None and node.left is None:
                    parent_depth[0] = 1
                    continue

                left_ret = [None]
                right_ret = [None]
                stack_frame.append((State.EXIT, (node, parent_depth, left_ret, right_ret)))
                if node.left is not None:                    
                    stack_frame.append((State.ENTER, (node.left, left_ret, [None], [None])))
                if node.right is not None:
                    stack_frame.append((State.ENTER, (node.right, right_ret, [None], [None])))
            
            else:
                _, parent_depth, left_depth, right_depth = args
                child_depths = []
                for child_depth in (left_depth[0], right_depth[0]):
                    if child_depth is None:
                        continue
                    child_depths.append(child_depth)
                parent_depth[0] = min(child_depths) + 1
            
        return root_depth[0]
```
