# 何も見ずに解く

## 思ったこと
- まだペアになってない {([ を記録しておいて，])} が来たら記録から最後にみたものをとってきて，ペアなら記録から消してあげる
- 再帰でも書けそうだな，でも N=10^4 でデフォルトの python の再帰のスタック上限こえそうだな
- ループで解く

時間計算量 O(N) 空間計算量 O(N)

```py
class Solution:
    def isValid(self, s: str) -> bool:
        complements = {"}": "{", "]": "[", ")": "("}

        waiting_for_pair = []

        for bracket in s:
            if bracket in "{([" or waiting_for_pair == []:
                waiting_for_pair.append(bracket)
                continue

            candidate =  waiting_for_pair.pop()
            if candidate != complements[bracket]:
                return False
        
        if waiting_for_pair == []:
            return True

        return False
```

