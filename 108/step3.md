# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
- 再帰によるDFS

```py
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None

        def build(first: int, last: int) -> Optional[TreeNode]:
            if first > last:
                return None

            mid = (first + last) // 2
            node = TreeNode(nums[mid])
            node.left = build(first, mid - 1)
            node.right = build(mid + 1, last)
            return node

        return build(0, len(nums) - 1)
```
