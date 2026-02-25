# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```py
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if k < 0:
            raise ValueError("k is out of range")

        def _update_visited_candidates(i: int, j: int) -> None:
            if i >= len(nums1) or j >= len(nums2) or (i, j) in visited:
                return 
            heapq.heappush(candidates, (nums1[i] + nums2[j], i, j))
            visited.add((i, j))
        
        visited = set()
        candidates = []
        smallest_pairs = []

        _update_visited_candidates(0, 0)
        while len(smallest_pairs) < k and candidates:
            _, i, j = heapq.heappop(candidates)
            smallest_pairs.append([nums1[i], nums2[j]])
            _update_visited_candidates(i + 1, j)
            _update_visited_candidates(i, j + 1)
        
        return smallest_pairs
```
