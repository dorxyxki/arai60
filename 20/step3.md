# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```py
class Solution:
    def isValid(self, s: str) -> bool:
        open_to_close = {
            "{": "}",
            "[": "]",
            "(": ")"
        }
        brackets = []

        for char in s:
            if char not in open_to_close and char not in open_to_close.values():
                raise ValueError("Input must consist of brackets: '{}', '()', '[]'")

            if char in open_to_close:
                brackets.append(char)
                continue
            
            if not brackets:
                return False
            
            bracket = brackets.pop()
            if char != open_to_close[bracket]:
                return False
            
        return not brackets
```