# 何も見ずに解く

Constraints:
    - 1 <= strs.length <= 10^4 : N
    - 0 <= strs[i].length <= 100 : M
    - strs[i] consists of lowercase English letters.

## 考え
- 一度見たことのあるグループに属すれば，そこに入れてあげる．そうでないならグループを作ってあげる．
    - 一度見たことのあるグループの管理を dict で行う．キーに代表，値にグループを．
    - 同じグループの判定にかかる時間計算量
        - 文字列を代表するものをソートしたものに
            時間計算量 O(MlogM) 空間計算量 O(M)
        - 文字列の並び替えの組み合わせを列挙したものを代表にする？
            時間計算量 O(M!) 空間計算量 O(M!)
            - 100! = 9*10^157 で現実的ではない (10! ≒ 3*10^6)

## 1-1 

時間計算量 O(NMlogM) 空間計算量 O(NM)

```py
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        sorted_to_anagrams = defaultdict(list)

        for original_str in strs:
            sorted_str = "".join(sorted(original_str))
            sorted_to_anagrams[sorted_str].append(original_str)

        return list(sorted_to_anagrams.values())
```
