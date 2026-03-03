# 他の方の解法やディスコのログを見て解く

## 他の解法
- どちらも，ユニークな数字の集合に変形して，集合同士の共通部分をとる
    - set の interserction or & をおこなう
        - set への変換: 時間計算量 O(N + M) 空間計算量 O(N + M)
        - intersection: 平均時間計算量 O(min(N, M)) 空間計算量 O(min(N, M))

- set を一つで済ませる手法
    - https://github.com/tarinaihitori/leetcode/pull/13/changes/fae7fb0f54e52aa1eb8ad0e81eea35c1fa4d2a48#r1826989238

- 追加質問について
    - https://github.com/katataku/leetcode/pull/12#discussion_r1893968021
        > たとえば、追加質問で考えられるのは、「片方がとても大きくて、片方がとても小さいときには、大きい方を set にするのは大変じゃないでしょうか、特に大きいほうが sort 済みのときにはどうしますか。」とかです。
    - 条件が変わった時にどんな解法をとるのかを感じられる余裕があれば理想なのか

## 参考
- set 
    - intersection, & による共通部分の作成
        - https://docs.python.org/3/library/stdtypes.html#frozenset.intersection
            > Note, the non-operator versions of union(), intersection(), difference(), symmetric_difference(), issubset(), and issuperset() methods will accept any iterable as an argument. In contrast, their operator based counterparts require their arguments to be sets.
        - intersection は iterable も受け取れる．
        - 平均計算量 O(min(N, M)) 最悪 O(N * M)
            - 片方が set ではない場合， 平均計算量 O(max(N, M)) 
            - https://wiki.python.org/moin/TimeComplexity

    - 公式実装をのぞいてみる
        - set_intersection をみると，両方setなら短い方を走査するようにされていて 平均計算量 O(min(N, M)) になる．
        - 一方で，実装だけ見ると片方 set ではない時，その長さをMとおくと O(M) では？ （O(max(N, M)) ではなく）
            - https://github.com/python/cpython/blob/c9a5d9aae48a9faa553a5e8137ff1b5e261f6bf6/Objects/setobject.c#L1675-L1755
        - & はほとんど intersection のラッパーか
            - https://github.com/python/cpython/blob/c9a5d9aae48a9faa553a5e8137ff1b5e261f6bf6/Objects/setobject.c#L1831-L1843

- nan, inf の挙動について
    - https://discord.com/channels/1084280443945353267/1201211204547383386/1208701087264280596
    - https://discord.com/channels/1084280443945353267/1200089668901937312/1200706702073401384
    - https://github.com/ksaito0629/leetcode_arai60/pull/12/changes#diff-d5203cc6e5aacffb982faf78b89db4a95cf27c1a9609008c47637168c8642c14R36
    - 知らなかった
        ``` 
        >>> a = float('inf')
        >>> a // 1
        nan
        >>> a % 1
        nan
        >>> a * 0
        nan
        >>> b = float('inf')
        >>> a == b
        True
        >>> a != b
        False
        ```

- サイズの大小の書き方の選択肢
    - https://github.com/yas-2023/leetcode_arai60/pull/13/changes#r2423403978
        > short_nums, long_nums = sorted([nums1,nums2], key=len)

## 2-1
- 1-1 のリファクタ & set 一つ

時間計算量 O(N + M) 空間計算量 O(min(N, M))

```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:        
        if len(nums2) < len(nums1):
            nums1, nums2 = nums2, nums1

        seen = set(nums1)
        common = []
        for num in nums2:
            if num in seen:
                common.append(num)
                seen.remove(num)
            
            if not seen:
                break
        
        return common
```

## 2-2 
- 片方が長くてソートされている場合
- 二分探索
    - https://github.com/aki235/Arai60/pull/13/changes#diff-6c0496a496cb8f7a35d43bf248b1df530eadc13c9be2b8c1270a983021b9e762R74
    - 不変条件を意識するととのちのち整数を探す以外の探索をしたくなった時に応用できそう
        - https://discord.com/channels/1084280443945353267/1245404801177616394/1308062891609428028
    - 今回だとこんなかんじか
        - i <= left で x[i] はターゲットよりも小さい数字, right <= j で x[j] はターゲット以上の数字を保つ
        - mid は left < mid < right なるもの，mid が存在しなくなったら終了．
        - mid は未確定領域のどこかでこれとターゲットの比較で どちらかの二領域（left まで or right 以降）を拡大する
        - かならず right - left は減少する
    - 公式の bisect_left は i < left で x[i] がターゲットよりも小さいという不変条件になてる．
        https://github.com/python/cpython/blob/3c19c88fa96eef41512ce6b65658d5b3b46bfcd6/Lib/bisect.py#L74-L107

M >= N として
時間計算量 O(NlogM) 空間計算量 O(N)
set をなくせば， 時間計算量 O(NlogM + N^2) 空間計算量 O(1)

```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:        
        if len(nums2) < len(nums1):
            nums1, nums2 = nums2, nums1
        sorted_nums2 = sorted(nums2)

        common_nums = set()
        for num in nums1:
            if num in common_nums:
                continue

            left = -1
            right = len(sorted_nums2)
            while right - left > 1:
                middle = (right + left) // 2
                if sorted_nums2[middle] < num:
                    left = middle 
                else:
                    right = middle

            if right < len(sorted_nums2) and sorted_nums2[right] == num:
                common_nums.add(num)

        return list(common_nums)
```

## 2-3
- 両方が長くてソートされている場合
- ポインタを二つ動かしてマージソートみたいにやる

時間計算量 O(NlogN + MlogM + N + M) 空間計算量 O(M + N)

```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:        
        sorted_nums1, sorted_nums2 = sorted(nums1), sorted(nums2)
        i1, i2 = 0, 0
        common_nums = []

        while i1 < len(sorted_nums1) and i2 < len(sorted_nums2):
            if sorted_nums1[i1] < sorted_nums2[i2]:
                i1 += 1
                continue
            if sorted_nums2[i2] < sorted_nums1[i1]:
                i2 += 1
                continue
            
            assert sorted_nums1[i1] == sorted_nums2[i2]
            common_num = sorted_nums1[i1]
            common_nums.append(common_num)

            while i1 < len(sorted_nums1) and sorted_nums1[i1] == common_num:
                i1 += 1
            while i2 < len(sorted_nums2) and sorted_nums2[i2] == common_num:
                i2 += 1
            
        return common_nums
```
