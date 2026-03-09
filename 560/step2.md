# 他の方の解法やディスコのログを見て解く

## 別の解法
- 累積和を利用する + 終了点を基準に探索しつつ，見たことのある累積和を dict に記録しておく．
    - 部分配列の開始，終了のインデックスを first, last として，first-last の部分和を求めるなら 0-last の和から 0-(first-1) の和を除くことで求められる．
    - last を一つずつ走査する途中，0-last を dict に入れておけば後から O(1) で検索できる

- 先に累積和をすべてつくり，部分配列の先頭を基準に探索をおこなう方法もある．
    - https://github.com/kitano-kazuki/leetcode/pull/16/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R58

## 参考
- 解法のイメージ
    - https://discord.com/channels/1084280443945353267/1233603535862628432/1252232545056063548
        > 「鉄道があって、各駅間ごとの標高差が与えられます。標高差が ちょうど K であるようなすべての駅の組み合わせを列挙してください。」
    - https://discord.com/channels/1084280443945353267/1183683738635346001/1192145962479665304
        > いや、累積和を日常で見る機会ってあると思うんですよ。たとえばですけれども、電車の各駅の距離とかかる時間が書かれていて、ちょうど10分かかる駅の組み合わせはどれか、といわれたら、(これはマイナスが出ないので更に楽ですが、)終着駅から出発する電車が、どこを何時何分に通過するかを書き出しながら、その10分前に別の駅にいたかを確認したらいいですよね。
    - 具体的なケースがシュッと出てくるようになりたい

- prefix_sum_to_count の更新をはじめに行う選択肢も．（直感的ではない）
    - https://github.com/katataku/leetcode/pull/15/changes#r1898173625
    - https://github.com/naoto-iwase/leetcode/pull/16/changes#diff-ca55fd85e2a5ebb9c7eaf31501a6d66a171f7c5ba74871f33d69cc1eb20dc00eR84

- 累計和 0 として番兵をおいておくと 2-1 で書くような `prefix_sum == k` といった分岐を減らせる．
    - https://github.com/kitano-kazuki/leetcode/pull/16/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R96
    - https://github.com/Kaichi-Irie/leetcode-python/pull/26/changes#r2514145142
    - https://discord.com/channels/1084280443945353267/1233603535862628432/1252251841576833136
        > はじめ標高0です。そっから、ひと駅ずつ移動するごとの標高差が与えられます。高さの差が K であるような、駅の組をすべて求めてください。
    - 初めは標高 0 と考えると番兵を置くのも自然におもえる．

- defaultdict の初期化
    - https://github.com/aki235/Arai60/pull/16/changes#r2807982139
        > prefix_sum_to_frequency = defaultdict(int, {0: 1})


## 2-1
- 一旦解法をコードにしたもの

Time:  O(N)
Space: O(N)

```py
from collections import defaultdict


class Solution:
    def subarraySum(self, nums: list[int], k: int) -> int:
        prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)
        num_subarray_sum_to_k = 0

        for tail_subarray in nums:
            prefix_sum += tail_subarray
            if prefix_sum == k:
                num_subarray_sum_to_k += 1            
            num_subarray_sum_to_k += prefix_sum_to_count[prefix_sum - k]
            prefix_sum_to_count[prefix_sum] += 1

        return num_subarray_sum_to_k
```

## 2-2
- リファクタ


```py
class Solution:
    def subarraySum(self, nums: list[int], k: int) -> int:
        prefix_sum = 0
        prefix_sum_to_count = {0: 1}

        count = 0
        for num in nums:
            prefix_sum += num
            count += prefix_sum_to_count.get(prefix_sum - k, 0)
            prefix_sum_to_count.setdefault(prefix_sum, 0)
            prefix_sum_to_count[prefix_sum] += 1
        
        return count
```
