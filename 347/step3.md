# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```py
from collections import defaultdict


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_count = defaultdict(int)
        for num in nums:
            num_to_count[num] += 1
        
        unique_nums_sorted_by_count = sorted(num_to_count, key=num_to_count.get, reverse=True)
        return unique_nums_sorted_by_count[:k]
```

## 3-2
```py
from collections import Counter


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_count = Counter(nums)

        count_and_num = []
        for num, count in num_to_count.items():
            heapq.heappush(count_and_num, (count, num))
            while len(count_and_num) > k:
                heapq.heappop(count_and_num)
        
        return [num for _, num in count_and_num]
```