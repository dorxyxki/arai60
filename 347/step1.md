# 何も見ずに解く

## 考え
- K の値に想定外なものが来ると意味ない値返す，エラー吐かずに意味ないもの返すのは使う人が困るだろうからエラー吐くようにする
- 元の配列は宣言しない限り変えたくないな

- ざっと思いついた解法
    - 値：頻度の辞書を作成，それをソートして k 以降をだせばいいはず: 1-1
    - 値：頻度の辞書を作成，頻度をキーに heap にして k 回 pop する
        - heappush で 長さ k の minheap をつくり topk 作ろう: 時間計算量 (Nlog(k) + k): 1-2
        - heapify で 長さN の maxheap を初期化，k回 popする: 時間計算量 (N + klog(N)): 1-3

- タプルの比較ってどうしてるっけ？（インデックスが小さいものから比較するようになってるんだろうか.）

- あれ，同率頻度が k の付近にある場合ってどうするんだろ．nums=[1,1,1,2,2,2]  k = 1  みたいな時は．
    - その場合は値が大きいもの順に返すという宣言をするのがいいのだろうか？
    - そもそも同率頻度の時は同率として扱って，k 件以上の要素を返すということを宣言するのが一番しっくりくるか．

## 1-1
時間計算量 O(Nlog(N)) 空間計算量 O(N)

```py
from collections import defaultdict


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        if not 1 <= k <= len(nums):
            raise ValueError("k is out of range")

        number_to_frequency = defaultdict(int)
        for num in nums:
            number_to_frequency[num] += 1
        
        numbers_sorted_by_frequency = sorted(number_to_frequency.items(), key=lambda x: x[1])
        return [number for number, frequency in numbers_sorted_by_frequency[-k:]]
```

## 1-2
時間計算量 O(k + Nlog(k)) 空間計算量 O(N)

```py
from collections import defaultdict


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        if not 1 <= k <= len(nums):
            raise ValueError("k is out of range")
        
        number_to_frequency = defaultdict(int)
        for num in nums:
            number_to_frequency[num] += 1
        
        topk_frequent_nums = []
        for num, freq in number_to_frequency.items():
            heapq.heappush(topk_frequent_nums, (freq, num))
            while len(topk_frequent_nums) > k:
                heapq.heappop(topk_frequent_nums)
        
        return [num for freq, num in topk_frequent_nums]
```

## 1-3
時間計算量 O(N + klog(N)) 空間計算量 O(N)

```py
from collections import defaultdict


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        if not 1 <= k <= len(nums):
            raise ValueError("k is out of range")
        
        number_to_frequency = defaultdict(int)
        for num in nums:
            number_to_frequency[num] += 1
        
        topk_frequent_nums = []
        for num, freq in number_to_frequency.items():
            topk_frequent_nums.append((freq, num))
        
        heapq.heapify_max(topk_frequent_nums)        
        return [heapq.heappop_max(topk_frequent_nums)[1] for _ in range(k)]
```
