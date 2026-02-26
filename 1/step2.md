# 他の方の解法やディスコのログを見て解く

## 他の解法
- ソートした後に，二つのポインタを 左(最小)と右(最大)のインデックスにおく．
    - そうすると 左を右に動かすと 二つの和は必ず単調増加，右を左に動かすと必ず単調減少．
    - ソート済みの配列が最初から与えられているならこれの方がメモリ消費が少なくて済むのか．
    - https://github.com/ksaito0629/leetcode_arai60/pull/10/changes#diff-fb72ed84c22daaa065dd8aca8a790d2a4a20bd48c4db22d7605117f02be53c40R61
    - https://discord.com/channels/1084280443945353267/1183683738635346001/1187326805015810089
        > 私は紙と鉛筆でやるんだったら、カードをソートして、頭と尻から辿っていくと思いますね。

        > はじめに、一番初めと一番最後に着目します。着目しているものを足します。目標よりも小さかったら、前の方の着目しているのを一つ後ろにずらします。目標よりも大きかったら、後ろの方の着目しているのを一つ前にずらします。繰り返したら見つかるはずです。
        - ソートして単調性を利用できないかなーと一瞬おもったが，一番最後に着目することができなかった．
            - 単調増加においては，後ろから見ると単調減少なのだから，一番最後におくことも視野に入っていているべきだなあ

## 考え
- 対応するものが見つからなかった時の挙動として何を考えるか
    - 自分がケースとして選択しなかった，エラーを返すならどう言うコードを書くかまで視野をもつべきだな

## 参考
- enumerate
    - start 0 以外指定可能なんだ，しらず．
    - https://docs.python.org/3/library/functions.html#enumerate

- ValueError
    - https://docs.python.org/ja/3/library/exceptions.html#ValueError
        > 演算子や関数が、正しい型だが適切でない値を持つ引数を受け取ったときや、 IndexError のようなより詳細な例外では記述できない状況で送出されます。
    - 配列が必要な要素数を満たしていない場合とかのエラーを返したい時はこれを返しそう．けっこう広い範囲の例外をサポートしそう．

- dict の初期化について: {} か dict() 
    - https://docs.python.org/3/reference/expressions.html
        > An empty set cannot be constructed with {}; this literal constructs an empty dictionary.
    - https://github.com/t0hsumi/leetcode/pull/11#discussion_r1883735842
        > {}でもdictの初期化はできますね。（こちらは構文レベルでサポートされており、dict関数の呼び出しが分、ちょっとパフォーマンス的に良かったりもします。）
        - こう言うのどこから調べるんだろ

## 2-1
- ソートしてから二つのポインタを動かす．

時間計算量 O(NlogN) 空間計算量 O(N)

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        sorted_nums = sorted([(num, i) for i, num in enumerate(nums)])

        min_index = 0
        max_index = len(nums) - 1

        while min_index < max_index:
            min_num, min_original_index = sorted_nums[min_index]
            max_num, max_original_index = sorted_nums[max_index]
            sum_num = min_num + max_num

            if sum_num == target:
                return [min_original_index, max_original_index]

            if sum_num > target:
                max_index -= 1
            if sum_num < target:
                min_index += 1
        
        raise []
```

## 2-2
- 1-1 のリファクタ

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        num_to_index = {}
        for i, num in enumerate(nums):
            complement = target - num
            if complement in num_to_index:
                return [num_to_index[complement], i]
            num_to_index[num] = i

        raise ValueError("No pair found that sums to target")
```
