# 何も見ずに解く

## 考え
- k が想定外の時弾くのがいいかな．

- 全ての組み合わせを作成し，それらの合計をkeyに k の大きさの MaxHeapを構成していく．
    - 時間計算量 O（MNlogk） 空間計算量O(k)
    - N<=10^5,M<=10^5, k<=10^4
    - なので，大体 cpython で 10^6 step/ s だとすると 1.5*10^5 秒かかるので，現実的じゃない．

- 値は増加するので，それぞれの配列におけるインデックスを小さい順から増やしてみていくのが効率良さそう．(0,0) は必ずペアとして入るはずだし．
    - 最初の方は次が大きいかどうかくらいの比較で済むが，あとの方になると，過去に探索したもの同士でペアになる可能性が出てくる．
    - わからず．

## 1-1
- 一度全探索してから small_k を出す

時間計算量 O（MNlogk） 空間計算量O(k)
TLE

```py
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if not 1 <= k <= len(nums1) * len(nums2):
            raise ValueError("k is out of range")

        smallest_k_pairs = []
        for num1 in nums1:
            for num2 in nums2:
                heapq.heappush_max(smallest_k_pairs, (num1+num2, num1, num2))
                if len(smallest_k_pairs) > k:
                    heapq.heappop_max(smallest_k_pairs)
        
        return [[num1, num2] for sum, num1, num2 in smallest_k_pairs]
```
