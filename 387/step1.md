# 何も見ずに解く

## 考え
- 一度目の走査で dict の構築(文字列：[カウント，最小のインデックス])をする．もう一度走査してカウントが1のインデックスの値を返す．
    - python の dict は挿入順を保つので, カウントが1の最初の要素が答えになる．（挿入順のバージョンは後で参照）

- 見たことのある文字の集合と，ユニークな文字のみをキーにもつ辞書を管理する
    - python の dict の挿入順を同様に利用

## 1-1

Time O(N + N)
Space O(N)

```py
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_to_count_and_index = dict()

        for i, ch in enumerate(s):
            char_to_count_and_index.setdefault(ch, [0, i])
            char_to_count_and_index[ch][0] += 1

        for count, index in char_to_count_and_index.values():
            if count == 1:
                return index

        return -1
```

## 1-2

Time:  O(N)
Space: O(N + N)

```py
class Solution:
    def firstUniqChar(self, s: str) -> int:
        candidate_char_to_index = {}
        seen_chars = set()

        for i, ch in enumerate(s):
            if ch not in seen_chars:
                seen_chars.add(ch)
                candidate_char_to_index[ch] = i
                continue
            
            if ch in candidate_char_to_index:
                del candidate_char_to_index[ch]
        
        if not candidate_char_to_index:
            return -1             
        return next(iter(candidate_char_to_index.values()))
```
