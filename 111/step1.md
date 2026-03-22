# 何も見ずに解く

## 考え

DFS
- 行きがけに 葉っぱに当たり次第 min_depth を更新していく
  - stack + loop の方がいいかな
- 帰りがけに 子供の min_depth に +1 をして上に上げるか？
  - これは再帰で書いた方がスッキリかけるか

BFS
- 最初に葉っぱに当たった時点の depth が min_depth になり，DFSより効率的か

## 1-1
- BFS 

Time:  O(N)
Space: O(N)

```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:        
        if root is None:
            return 0

        def is_leaf(node: Optional[TreeNode]) -> bool:
            return node.left is None and node.right is None

        depth = 0
        frontier = [root]
        while frontier:
            next_frontier = []
            depth += 1
            for node in frontier:
                if node is None:
                    continue
                if is_leaf(node):
                    return depth
                next_frontier.append(node.left)
                next_frontier.append(node.right)
            frontier = next_frontier
```

## 1-2
- スタックを使った DFS 
    - min_depth を float の inf で定義する部分は避けたいなと思うが，これ以上思いつかず
        - コードを読まないと，min_depth 初期化の部分で，float("int") が帰るのではないかと一瞬気になるはず

Time:  O(N)
Space: O(N)

```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        def is_leaf(node: Optional[TreeNode]) -> bool:
            return node.left is None and node.right is None
        
        min_depth = float("inf")
        node_to_visit = [(root, 1)]
        while node_to_visit:
            node, depth = node_to_visit.pop()
            if node is None:
                continue
            if is_leaf(node):
                min_depth = min(min_depth, depth)
                continue
            node_to_visit.append((node.left, depth + 1))
            node_to_visit.append((node.right, depth + 1))
        return min_depth
```

## 1-3
- 再帰 DFS 

Time:  O(N)
Space: O(N)

```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0
        
        if root.left is None and root.right is None:
            return 1
            
        left_min_depth = float("inf")
        right_min_depth = float("inf")
        if root.left is not None:
            left_min_depth = min(left_min_depth, self.minDepth(root.left))
        if root.right is not None:
            right_min_depth = min(right_min_depth ,self.minDepth(root.right))
        return min(left_min_depth, right_min_depth) + 1
```
