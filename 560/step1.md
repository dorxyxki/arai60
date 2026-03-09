# 何も見ずに解く

## 考え
- subarray の和を 愚直に 二重loop で見ていく． 
    - 和を計算する時に sum(nums[i:j+1]) で全体の 時間計算量 O(N^3) になるかな？
    - 和をloopで逐次更新すれば 時間計算量 O(N^2) か
    - CPython が 10^7 step / sec 処理するとして N <= 2*10^4 なので 大体全部で 40 sec くらいなので TLE になりそう

- nums([i:j+1]) の和をもとめるときに，sum(nums) - sum(nums[:i]) - sum(nums[j+1:]) みたいに他の情報から出してあげれば効率的になるかな？
    - これ以上わからず

## 1-1

Time:  O(N^2)
Space: O(1)

TLE になる．

```py
class Solution:
    def subarraySum(self, nums: list[int], k: int) -> int:
        num_equal_subarray = 0

        for i in range(0, len(nums)):
            sum_subarray = 0

            for j in range(i, len(nums)):
                sum_subarray += nums[j]
                if sum_subarray == k:
                    num_equal_subarray += 1

        return num_equal_subarray
```
