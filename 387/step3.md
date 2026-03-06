# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

Time O(N)
Space O(1)

```py
from collections import defaultdict


class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_count = defaultdict(int)
        for ch in s:
            char_to_count[ch] += 1
        
        for i, ch in enumerate(s):
            if char_to_count[ch] == 1:
                return 1
        return -1
```
