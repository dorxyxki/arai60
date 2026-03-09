# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

```py
from collections import defaultdict


class Solution:
    def subarraySum(self, nums: list[int], k: int) -> int:
        prefix_sum = 0
        prefix_sum_to_count = defaultdict(int, {0: 1})

        count = 0
        for num in nums:
            prefix_sum += num
            count += prefix_sum_to_count[prefix_sum - k]
            prefix_sum_to_count[prefix_sum] += 1
        
        return count
```
