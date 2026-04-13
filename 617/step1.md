# 何も見ずに解く

## 考え

- マージ元どちらか片方でも存在してたらそのポジションを探索し続けるのか？

- マージ済みのあるノードの位置に注目する
    - その位置の左を見に行く
        - マージ元がどちらも存在している場合、マージノードを作成して左先にくっつける
        - 片方のマージ元が存在している場合、それ以降の木のコピーを丸ごと左先にくっつける
        - どちらもなければ何もくっつけない。
    - 右も同様に
- どちらも存在している場合だけ探索を進める

- これだと bfs で解けそう

## 1-1

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

from collections import deque


class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 is None and root2 is None:
            return None

        def merge_tree(node1: TreeNode | None, node2: TreeNode | None) -> TreeNode | None:
            if node1 is None and node2 is None:
                return None

            if node1 is None:
                return copy.deepcopy(node2)
            if node2 is None:
                return copy.deepcopy(node1)
            if node1 is not None and node2 is not None:
                return TreeNode(node1.val + node2.val)

        merged_root = merge_tree(root1, root2)
        frontier = deque()
        if root1 is not None and root2 is not None:
            frontier.append((merged_root, root1, root2))

        while frontier:
            node, node1, node2 = frontier.popleft()
            node.left = merge_tree(node1.left, node2.left)
            node.right = merge_tree(node1.right, node2.right)
            if node1.left is not None and node2.left is not None:
                frontier.append((node.left, node1.left, node2.left))
            if node1.right is not None and node2.right is not None:
                frontier.append((node.right, node1.right, node2.right))

        return merged_root
```

