# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

時間計算量 O(N + M) 空間計算量 O(min(N, M))

```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:        
        if len(nums2) < len(nums1):
            nums1, nums2 = nums2, nums1
        
        seen_nums = set(nums1)
        common_nums = []

        for num in nums2:
            if num in seen_nums:
                common_nums.append(num)
                seen_nums.remove(num)

            if not seen_nums:
                break
        
        return common_nums
```
