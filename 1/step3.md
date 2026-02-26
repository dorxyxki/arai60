# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen_num_to_index = {}
        for i, number in enumerate(nums):
            complement = target - number
            if complement in seen_num_to_index:
                return [seen_num_to_index[complement], i]
            seen_num_to_index[number] = i

        raise ValueError(f"No such pair that sums to target: {target}")
```
