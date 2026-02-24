# 他の方の解法やディスコのログを見て解く

## 他の解法
- quick select を使用する
    - partition によって pivot より左右が大きい or 小さい になるので，pivot が 適した位置にくるまで片方を partition し続ければ topkが出ると
    - https://github.com/kitano-kazuki/leetcode/pull/9/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R111
    - https://discord.com/channels/1084280443945353267/1183683738635346001/1185972070165782688
        > Quick Select も知っていていいんじゃないでしょうか。ただ、書くと大変ですね。
    - ついでに quick sort の復讐もしておく
        > クイックソートについて知っていなくてはいけないことは
        > 最悪・平均計算量
        > 末尾再帰最適化
        > ピボット選択
        > マージソートとのプロコン
        > あたりです。
        - GPTにまとめてもらっていたPR: https://github.com/naoto-iwase/leetcode/pull/9/changes#diff-2d1f664c4bc1102fa070db0b0b8329b974594503435ab3437b253c140ebb78f1R126
            > **ランダム pivot**
            > 偏りを確率的に避ける。理論的に平均 O(n log n) を保証。
            > * **中央値近似 pivot**
            > first/middle/last の中央値（median-of-three）で分割バランスを良くする。

            > クイックソートが実際に速い理由は：
            > * 比較回数は同程度でも、メモリアクセスが局所的（キャッシュヒット率が高い）
            > * 分割のたびに大きな連続領域を順走査するだけ
            > → CPU キャッシュと分岐予測に優しい。


- bucket sort
    - 頻度みたいに狭い範囲の数値かつ整数の評価値で並び替えたいってなったらインデックスが評価値になってるバケットソートの考え方が出てきても違和感ない．
    - https://github.com/kitano-kazuki/leetcode/pull/9/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R69

- collections.Counter を使用する(やってることは priority queue とかわらない)
    - https://github.com/kitano-kazuki/leetcode/pull/9/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R69
    - most_common を使用する方法もあるが，ほぼ heapq の nlargest をラップしてるだけ
        - https://docs.python.org/3/library/collections.html#collections.Counter.most_common
        - https://github.com/python/cpython/blob/fd0400585eb957c7d10812d87a8cb9e1f3c72519/Lib/collections/__init__.py#L625

- heapq.nlargest をつかう
    - key を比較基準として 上位 k の minheap を管理する．時間計算量 O(Nlogk) になってる．
    - https://github.com/python/cpython/blob/fd0400585eb957c7d10812d87a8cb9e1f3c72519/Lib/heapq.py#L537

- 平衡二分探索木を利用する
    - ストリーミング形式で topk がほしいと言われたと考えると自然に出てきてほしい考え方なのか．
    - ref: https://discord.com/channels/1084280443945353267/1227073733844406343/1230725828917198950


## 参考
- iter(dict) の挙動
    - https://docs.python.org/3/library/stdtypes.html#typesmapping
        > iter(d)
        > Return an iterator over the keys of the dictionary. This is a shortcut for iter(d.keys()).

- シーケンス型の比較は辞書順におこなう．
    > Sequences compare lexicographically using comparison of corresponding elements.
    - https://docs.python.org/ja/3/reference/expressions.html#value-comparisons

- defaultdict の挙動確認    
    - dictの __getitem__() が呼ばれて key ないときに __missing__() が呼ばれ，そこで設定した関数が引数なしで呼ばれ，デフォルト値として設定されて，値が返されると．
        - https://docs.python.org/3/library/collections.html#defaultdict-objects
        - https://github.com/sakupan102/arai60-practice/pull/10/changes#diff-5cd4e95016aaca2d8bd5031dc91d4cc73ac3baad3b6c002619c9ceb3f0a9fd5fR82

- Counter と defaultdict のちがい
    - Counterは __missing__ が呼ばれた時にデフォルト値を設定しない
        ```
        >>> from collections import Counter
        >>> from collections import defaultdict
        >>> A = defaultdict(int)
        >>> A["c"]
        0
        >>> A
        defaultdict(<class 'int'>, {'c': 0})
        >>> B = Counter(a=10, b=1)
        >>> B["c"]
        0
        >>> B
        Counter({'a': 10, 'b': 1})
        ```
    - https://github.com/python/cpython/blob/fd0400585eb957c7d10812d87a8cb9e1f3c72519/Lib/collections/__init__.py#L616
        ```py
        def __missing__(self, key):
            'The count of elements not in the Counter is zero.'
            # Needed so that self[missing_item] does not raise KeyError
            return 0
        ```
    
- sorted の key について
    - key に設定できるのは，引数を一つ取る function or callable で，その返り値で比較する．
        - https://docs.python.org/3/howto/sorting.html
        - https://github.com/fuga-98/arai60/pull/10/changes/BASE..7c2261690368bcd90655834b6730e0ff88d534ee#diff-115377fc1006f480f5d69dc82bc2a591997b21b396249ef4101ec3411bf2d40dR52
            > sorted(num_count, key=num_count.get, reverse=True) で、key だけが並んでくれるでしょう。
        - ちゃんとドキュメント読んでればこう書くのもすっとはいってくる．
        - 思考停止で lambda x:x[0] みたいに書いてしまっていたな．
    - lambda x:x[0] は itemgetter(0) みたいに書くことも
        - https://docs.python.org/3/howto/sorting.html#operator-module-functions-and-partial-function-evaluation
            > from operator import itemgetter, attrgetter

            > sorted(student_tuples, key=itemgetter(2))
            >[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]

- heapq.nlargest
    - order を加えて管理することで同値でも元のリストの順序を保つようにしてる．
        - https://docs.python.org/3/library/heapq.html#heapq.nlargest
            > Equivalent to: sorted(iterable, key=key, reverse=True)[:n].
        - https://github.com/python/cpython/blob/fd0400585eb957c7d10812d87a8cb9e1f3c72519/Lib/heapq.py#L537

- 出題の意図
    - https://discord.com/channels/1084280443945353267/1235829049511903273/1245555256360697949
        > 出題者の意図的には、dict の初期化をして数え、その後 list にして sorted に key function を渡して k 個取る、くらいが想定されているように見えます。
        > Counter を使うと、さすがに Counter の内部実装を書いて欲しいといわれるものと思います。
        > で、そこから Quick select に手を出すのもいいですが、Python で書いても Native 実行の sorted に速度でおそらく劣り、(log はなかなか定数倍に追いつかない)またコードも複雑なので選択しないです。
        > あとは、priority_queue ですね

## 2-1
- dict + sort

```py
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        if not 1 <= k <= len(nums):
            raise ValueError("k is out of range")
        
        num_to_count = dict()
        for num in nums:
            num_to_count[num] = num_to_count.get(num, 0) + 1
        
        nums_sorted_by_count = sorted(num_to_count, key=num_to_count.get, reverse=True)
        return nums_sorted_by_count[:k]
```

## 2-2
- dict + heap

```py
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        if not 1 <= k <= len(nums):
            raise ValueError("k is out of range")

        num_to_count = dict()
        for num in nums:
            num_to_count[num] = num_to_count.get(num, 0) + 1


        topk_counted_nums = []
        for num, count in num_to_count.items():
            heapq.heappush(topk_counted_nums, (count, num))
            while len(topk_counted_nums) > k:
                heapq.heappop(topk_counted_nums)

        return [num for count, num in topk_counted_nums]
```

## 2-3
- bucket sort の考え方を利用したもの
平均時間計算量 O(N) 空間計算量 O(N)

```py
from collections import Counter


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        if not 1 <= k <= len(nums):
            raise ValueError("k is out of range")

        num_to_count = Counter(nums)
        bucket_for_each_count = [[] for _ in range(len(nums) + 1)] 

        for num, count in num_to_count.items():
            bucket_for_each_count[count].append(num)
        
        topk_nums = []
        for nums in reversed(bucket_for_each_count):
            for num in nums:
                topk_nums.append(num)
                if len(topk_nums) >= k:
                    return topk_nums
```


## 2-4
- quick select
- ref:
    - partition の際に stored_index という変数を使用しているのがよくみられた．これは pivot の左側にくる値を保存する用(まだそこには確定していない値が入ってる)のインデックスという視点で見るととてもわかりやすい．
    - https://github.com/ichika0615/arai60/pull/7/changes#diff-e3f37462765cfdb7f4c96fe7709ec227852982f43da62904f61487b896733300R155
    - https://github.com/naoto-iwase/leetcode/pull/9/changes#diff-2d1f664c4bc1102fa070db0b0b8329b974594503435ab3437b253c140ebb78f1R229
    - https://github.com/tokuhirat/LeetCode/pull/9/changes#r2074951611

- quick select は quick sort のように pivot で別れた片方だけの処理を続ければいいので，iterative に書き下せる．
- これが quick sort の場合，配列長が短い方だけを recursive に書いて，長い方は iterative で処理すればいいのか

平均時間計算量 O(N) 最悪時間計算量 O(N^2) 空間計算量 O(N)

```py
import random
from collections import defaultdict


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_count = defaultdict(int)
        for num in nums:
            num_to_count[num] += 1
        unique_nums = list(num_to_count.keys())

        def recursively_quickselect(left: int, right: int) -> None:
            if left >= right:
                return None

            pivot_index = _partition(left, right)
            if pivot_index == k - 1:
                return None
                
            if pivot_index > k - 1:
                recursively_quickselect(left, pivot_index - 1)
            else:
                recursively_quickselect(pivot_index + 1, right)

        def iteratevely_quickselect(left: int, right: int) -> None:
            while left < right:
                pivot_index = _partition(left, right)
                if pivot_index == k - 1:
                    return 
                
                if pivot_index > k - 1:
                    right = pivot_index - 1
                else:
                    left = pivot_index + 1

        def _partition(left: int, right: int) -> int:
            pivot = random.randint(left, right)
            _swap(pivot, right)
            pivot = right
            
            reserved_left_next_index = left
            for index in range(left, right):
                if _get_count(index) > _get_count(pivot):
                    _swap(reserved_left_next_index, index)
                    reserved_left_next_index += 1
            
            _swap(reserved_left_next_index, pivot)
            pivot = reserved_left_next_index
            return pivot

        def _swap(i: int, j: int) -> None:
            unique_nums[i], unique_nums[j] = unique_nums[j], unique_nums[i]

        def _get_count(index: int) -> int:
            return num_to_count[unique_nums[index]]

        iteratevely_quickselect(0, len(unique_nums) - 1)
        return unique_nums[:k]
```
