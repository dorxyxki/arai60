# 何も見ずに解く

N <= 10^4 

## 考え
どちらか2通りの書き方がありそう

- BFS:
    - layer-wise に for loop でみていく．最終層で max depth
    - queue: depth も一緒に管理

- DFS: 
    - 再帰: 左右のうち，大きい方をとる．
    - stack: depth も一緒にと管理

## 1-1
- 再帰は sys.setrecursionlimit() ができない環境だと，制約がデフォルト制限の 1000 を超えてくるので，選ばないかな

Time:  O(N)
Space: O(N)

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        frontier_and_depth = [(root, 1)]
        def add_child(node: TreeNode, depth: int) -> None:
            if node.left is not None:
                frontier_and_depth.append((node.left, depth + 1))
            if node.right is not None:
                frontier_and_depth.append((node.right, depth + 1))

        max_depth = 0
        while frontier_and_depth:
            node, depth = frontier_and_depth.pop()
            max_depth = max(depth, max_depth)
            add_child(node, depth)
        return max_depth
```

## 1-2
- BFS の queue なし

Time:  O(N)
Space: O(N)

```py
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
            
        frontier = [root]
        depth = 0
        while frontier:
            next_frontier = []
            depth += 1
            for node in frontier:
                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)     
            frontier = next_frontier
        return depth
```

## 1-3
- 再帰で書く

Time:  O(N)
Space: O(N)

```py
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        return max(self.maxDepth(root.left), self.maxDepth(root.right)) + 1
```
