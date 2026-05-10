# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

```py
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        node_and_total = [(root, 0)]

        while node_and_total:
            node, path_sum = node_and_total.pop()
            if node is None:
                continue
            
            path_sum += node.val
            if node.left is None and node.right is None:
                if path_sum == targetSum:
                    return True
            
            node_and_total.append((node.right, path_sum))
            node_and_total.append((node.left, path_sum))
        
        return False
```
