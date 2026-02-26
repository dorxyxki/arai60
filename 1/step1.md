# 何も見ずに解く

## 考え
- 解き方は2通りくらいしか思いつかなかった．
    - 全部の組み合わせを前から確認していく．時間計算量 O(N^2) 空間計算量O(1) で，100 s くらいかかってTLEになるかも
    - 一度見た数を dict に保存して，今見ている値の補完値になっていればおわり．時間計算量 O(N) 空間計算量 O(N)

- 条件を満たすペアが見つかりずらいケースを考えて，見つからなかった場合，エラーを返すより，空配列を返すのがいいかなとおもった

## 1-1

時間計算量 O(N) 空間計算量 O(N)

```py
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        seen_num_to_index = dict()
        for i, num in enumerate(nums):
            complement = target - num
            if complement in seen_num_to_index:
                return [seen_num_to_index[complement], i]

            seen_num_to_index[num] = i

        return []
```
