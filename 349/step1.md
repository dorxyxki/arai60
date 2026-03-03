# 何も見ずに解く

## 考え
-  nums1, nums2 を集合に変換して，集合の共通部分を返す
    - 時間計算量 O(N + M + max(N, M)?) 空間計算量 O(N + M)
        - 共通部分の時間計算量を知らないので後で調べる

- 短い配列を集合に変換，長い配列を走査する時に集合に含まれるかを見る
    - 時間計算量 O(N + M) 空間計算量 O(min(N, M) + min(N, M))

## 1-1
- 二つ目で解く
時間計算量 O(N + M) 空間計算量 O(min(N, M) + min(N, M))

```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:        
        shorter, longer = nums1, nums2
        if len(nums2) < len(nums1):
            shorter, longer = nums2, nums1

        seen = set(shorter)
        common = set()
        for num in longer:
            if num in seen:
                common.add(num)
        
        return list(common)
```