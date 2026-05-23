# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```py
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []

        zigzag_values_by_level = []
        frontier = []
        left_to_right = True
        while frontier:
            next_frontier = []
            values = []

            for node in frontier:
                values.append(node.val)
                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)
            
            if not left_to_right:
                values.reverse()
            zigzag_values_by_level.append(values)
            frontier = next_frontier
            left_to_right = not left_to_right

        return zigzag_values_by_level
```
