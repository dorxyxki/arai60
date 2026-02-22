# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```py
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        if not 1 <= k <= len(nums) + 1:
            raise ValueError("k is out of range. k must be between 1 and len(nums) + 1")
        
        self._k = k
        self._topk_largests = []
        for num in nums:
            _ = self.add(num)

    def add(self, val: int) -> int:
        heapq.heappush(self._topk_largests, val)
        while len(self._topk_largests) > self._k:
            heapq.heappop(self._topk_largests)
        return self._topk_largests[0]
```
