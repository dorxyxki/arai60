# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

```py
from collections import defaultdict

class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        sorted_str_to_anagrams = defaultdict(list)
        for original_str in strs:
            sorted_str = "".join(sorted(original_str))
            sorted_str_to_anagrams[sorted_str].append(original_str)
        return list(sorted_str_to_anagrams.values())
```

## 3-2

```py
class Solution:
    def groupAnagrams(self, strs: list[str]) -> list[list[str]]:
        sorted_str_to_anagrams = dict()
        for original_str in strs:
            sorted_str = "".join(sorted(original_str))
            sorted_str_to_anagrams.setdefault(sorted_str, []).append(original_str)
        return list(sorted_str_to_anagrams.values())
```
