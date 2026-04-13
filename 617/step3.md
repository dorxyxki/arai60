# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

- 再帰で探索する、かつ新しいオブジェクトを返す方法

```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if root1 is None and root2 is None:
            return None
        if root1 is None:
            return copy.deepcopy(root2)
        if root2 is None:
            return copy.deepcopy(root1)
        
        merged_node = TreeNode(root1.val+root2.val)
        merged_node.left = self.mergeTrees(root1.left, root2.left)
        merged_node.right = self.mergeTrees(root1.right, root2.right)
        return merged_node
```
