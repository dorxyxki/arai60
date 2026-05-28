# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
- preordre + iterative

```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:        
        if root is None:
            return True
        
        node_with_bounds = [(root, float("-inf"), float("inf"))]
        while node_with_bounds:
            node, strict_lower_bound, strict_upper_bound = node_with_bounds.pop()
            if not (strict_lower_bound < node.val < strict_upper_bound):
                return False
            
            if node.right is not None:
                node_with_bounds.append((node.right, node.val, strict_upper_bound))
            if node.left is not None:
                node_with_bounds.append((node.left, strict_lower_bound, node.val))
        
        return True
```
