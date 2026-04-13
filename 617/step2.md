# 他の方の解法やディスコのログを見て解く

## 別の解法
- 繋げ方
    - マージしたノードを親に繋ぐという方法
        - 親から見てどっち側のノードなのかという情報と繋ぐ親を保存する必要がある
            - https://github.com/kitano-kazuki/leetcode/pull/23/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R129
            - https://github.com/tom4649/Coding/pull/22/changes#diff-74225fe221907aa6036c19abbfd87a9e33a2883c64b0538a6afe1a354955c573R1
    - マージしたノードを子供として繋ぐ方法

- 探索の限界
    - マージ元のノードがどちらも存在している位置だけを探索する
        - https://github.com/hayashi-ay/leetcode/pull/12/changes#diff-9bdf6ea146707cd300caa32b58d1af7701eda07a7d2e9802408cd8ce1cc70720R28
    - マージ元のノードが片方なくてもそれ以降の位置を探索し続ける
        - https://github.com/xbam326/leetcode/pull/25/changes#diff-be4b02f48ef56b8cc99f5eb0ff156b70867e9f020c598db9e2af3fc09f3b54a2R14

- 探索の手法
    - bfs
    - dfs
        - https://github.com/kitano-kazuki/leetcode/pull/23/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852L1-R528
        - stack を使う際、ボトムアップで親に繋いでいく
            - https://github.com/nanae772/leetcode-arai60/pull/23/changes#diff-a350d7aa031694f3530ff59c5c80cea7e5a8367e31f5bc3153529ef7d9bcb38dR1  
    - 再帰
        - https://github.com/Shunii85/arai60/pull/23/changes#diff-ba17b49801670d8e3f8b372847ec8bb7ec1967c96d5de43dc95e8e79bece3cd1R1
        - 

## 参考
- 新しいツリーを作るのではなく、片方に重ねてしまう方法
    - https://github.com/tarinaihitori/leetcode/pull/23/changes/BASE..3661cef8b334d992a50e919393c5db1b8e22f9e0#diff-1ede2b2a752e6743ca4d35b115594d80caecd186a464943ab76618d8d1811252R24
    - https://github.com/tom4649/Coding/pull/22/changes#diff-e4c307d0ba5efd0b1a36146115e91040e2648b135742b5ecc524ae94aac22fdaR1
    - 入力は破壊されるが、若干メモリ効率がいいというメリットがある

- `if node1 is None and node2 is None:` ここのコード必要ない
    - https://github.com/Shunii85/arai60/pull/23/changes#diff-b1d08e4d77e2f992323c919a01d63a59f8d9bf7271b218431844d4e356bb7beaR127
    - 明示的に書くのが好きな人も
        - https://github.com/5ky7/arai60/pull/24/changes#r2616572497

- copy.deepcopy
    - https://docs.python.org/3/library/copy.html#copy.deepcopy
        > A deep copy constructs a new compound object and then, recursively, inserts copies into it of the objects found in the original.

- 操作自体を stack に入れてしまう方法
    - https://discord.com/channels/1084280443945353267/1295357747545505833/1329983679765811292

- partial
    - https://docs.python.org/3/library/functools.html#functools.partial
        > The partial() function is used for partial function application which “freezes” some portion of a function’s arguments and/or keywords resulting in a new object with a simplified signature


## 2-1

- 親につなげる + マージ元のノードが片方でもあれば探索し続ける + dfs 

```py
class Solution:
    def mergeTrees(self, root1: TreeNode | None, root2: TreeNode | None) -> TreeNode | None:
        dummy = TreeNode()
        to_merge = [(root1, root2, dummy, "left")]

        while to_merge:
            node1, node2, parent, direction = to_merge.pop()
            if node1 is None and node2 is None:
                continue

            merged_node = TreeNode()
            merged_node.val += getattr(node1, "val", 0)
            merged_node.val += getattr(node2, "val", 0)
            setattr(parent, direction, merged_node)

            for child in ["left", "right"]:
                to_merge.append(
                    (
                        getattr(node1, child, None),
                        getattr(node2, child, None),
                        merged_node,
                        child
                    )
                )

        return dummy.left
```

## 2-2

- 親につなげる + マージ元のノードが片方でもあれば探索し続ける + stack & ボトムアップでの dfs 

```py
from enum import Enum, auto


class State(Enum):
    ENTER = auto()
    EXIT = auto()


class Solution:
    def mergeTrees(self, root1: TreeNode | None, root2: TreeNode | None) -> TreeNode | None:
        merged_root = [None]
        stack = [(State.ENTER, (root1, root2, merged_root, [None], [None]))]

        while stack:
            direction, args = stack.pop()
            node1, node2, ret, left_ret, right_ret = args

            if direction == State.ENTER:    
                if node1 is None and node2 is None:
                    continue
                
                left_slot = [None]
                right_slot = [None]
                stack.append(
                    (
                        State.EXIT, 
                        (node1, node2, ret, left_slot, right_slot)
                    )
                )
                stack.append(
                    (
                        State.ENTER, 
                        (getattr(node1, "left", None), getattr(node2, "left", None), left_slot, [None], [None])
                        )
                    )
                stack.append(
                    (
                        State.ENTER, 
                        (getattr(node1, "right", None), getattr(node2, "right", None), right_slot, [None], [None])
                    )
                )

            else: # State.EXIT
                merged_node = TreeNode()
                if node1 is not None:
                    merged_node.val += node1.val
                if node2 is not None:
                    merged_node.val += node2.val
  
                merged_node.left = left_ret[0]
                merged_node.right = right_ret[0]
                ret[0] = merged_node

        return merged_root[0]
```

## 2-3

- 子供に繋ぐ + マージ元のノードが片方でもあれば探索し続ける + bfs

```py
class Solution:
    def mergeTrees(self, root1: TreeNode | None, root2: TreeNode | None) -> TreeNode | None:        
        if root1 is None and root2 is None:
            return None

        def merge_node(node1: TreeNode | None, node2: TreeNode | None) -> TreeNode | None:
            if node1 is None and node2 is None:
                return None

            merged_node = TreeNode()
            merged_node.val += getattr(node1, "val", 0)
            merged_node.val += getattr(node2, "val", 0)
            return merged_node

        merged_root = merge_node(root1, root2)
        frontier = deque()
        frontier.append((root1, root2, merged_root))

        while frontier:
            node1, node2, merged_node = frontier.popleft()
            for child in ["left", "right"]:
                setattr(merged_node, child, merge_node(getattr(node1, child, None), getattr(node2, child, None)))
                if getattr(merged_node, child, None) is not None:
                    frontier.append((getattr(node1, child, None), getattr(node2, child, None), getattr(merged_node, child)))

        return merged_root
```

## 2-4

- 子供に繋ぐ + マージ元のノードが片方でもあれば探索し続ける + 再帰

```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 is None and root2 is None:
            return None
        
        merged_node = TreeNode()
        merged_node.val += getattr(root1, "val", 0)
        merged_node.val += getattr(root2, "val", 0)

        merged_node.left = self.mergeTrees(getattr(root1, "left", None), getattr(root2, "left", None))
        merged_node.right = self.mergeTrees(getattr(root1, "right", None), getattr(root2, "right", None))
        return merged_node
```
