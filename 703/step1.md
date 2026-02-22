# 何も見ずに解く

- 制約
    - N = nums.length <= 10^4
    - M = add <= 10^4
    - 1 <= k <= nums.length + 1

## 考え
- 初期化の時点で k 番目に大きい値以上のものだけを保存する．
    - ソートするなら時間計算量 O(NlogN)
- add の時に，保存しておいた配列の更新をして最小値を取ればいい．

add 1回ごとの時間計算量と全体のみつもり
- 保存する配列がソート済みなら挿入するインデックスの二部探索で O(logk) , 挿入の時に O(k) ? (要調査)
    - 更新と取得に O(k) かかると全部のaddで最大 10^8 ステップ
    - だいたい CPU のクロック周波数が 10^9 Hzとして，C++ でだいたいオーバーヘッド考えて 1 秒に 10^8 クロック
    - Python だと 100倍くらい遅くて 1秒に 10^6 クロックくらいと考えて，全部の操作で 100 秒くらい？

- 保存する配列にヒープを利用すれば，更新が O(logk) で済む
    - log2(10^4) ≒ 4*(1/0.301) <= 15 なので，全部の操作でだいたい 0.15 秒くらい．
    - ヒープの初期化の時間計算量は O(N)?（要調査）

総合で最悪時間計算量 O(Mlogk) 空間計算量 O(1)
## 1-1
```py
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        heapq.heapify(nums)
        for _ in range(len(nums) - k):
            heapq.heappop(nums)
        
        self.kth_largests = nums

    def add(self, val: int) -> int:
        if not self.kth_largests:
            heapq.heappush(self.kth_largests, val)
            return val

        if val <= self.kth_largests[0]:
            return self.kth_largests[0]

        heapq.heappush(self.kth_largests, val)
        heapq.heappop(self.kth_largests)
        return self.kth_largests[0]
```

見落としていた - k = nums.length + 1
の場合のケースでエラー

初期化の時点で，self.kth_largests には必ず k 個の要素がないといけないのを意識していなかった．
以下のように番兵を置いて解決することを考えたが，そもそも
```py
    def __init__(self, k: int, nums: List[int]):
        heapq.heapify(nums)
        for _ in range(len(nums) - k):
            heapq.heappop(nums)
        
        if len(nums) < k:
            heapq.heappush(nums, -float("inf"))
            
        self.kth_largests = nums
```
