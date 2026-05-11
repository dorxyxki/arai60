# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

```py
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        frontier = [root]
        values_by_level = []
        while frontier:
            next_frontier = []
            values = []
            for node in frontier:
                values.append(node.val)
                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)
            
            frontier = next_frontier
            values_by_level.append(values)

        return values_by_level
```