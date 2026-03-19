# 何も見ずに解く

## 考え
- N <= 5000
- len(word) = L <= 10 

- 単語から次の単語までの遷移グラフを作成する（単語をノード，エッジが一文字違い）
- 始点ノードから終点ノードまでBFSで探索，最初に見つかった単語までの深さが最小のパス．
    - ダイクストラ（空書きできない）でも解けそう．（エッジに重みがないので今回はBFSでいいか）

- グラフをどう作成するか：
    - 愚直に2回ワードリストを走査して隣接リストを作っていく
        - 一文字違いの判定でO(s) か
        - Time O(L * N^2) で 2.5 × 10^8 cpython だと 25 s くらいでTLEしそう

- BFSで探索をする
    - 始点ノードからの深さも一緒に queue で管理してあげるのが良さそう
    - Time O(N^2) (これは間違い，今回のケースだと，エッジの多さはたかだか NL になるので，O(NL))

## 1-1 
TLE

Time:  O(LN^2)
Space: O(N + E)

```py
from collections import defaultdict, deque


class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        
        def different_by_single_letter(word1: str, word2: str) -> bool:
            if len(word1) != len(word2):
                return False
            count = 0
            for i, ch in enumerate(word1):
                if word2[i] != ch:
                    count += 1
            return count == 1

        word_to_adjacent_words = defaultdict(list)
        def connect_words(wordlist: list, begin_word: str) -> None:
            for i, word1 in enumerate(wordlist):
                for j, word2 in enumerate(wordlist[:i]):
                    if different_by_single_letter(word1, word2):
                        word_to_adjacent_words[word1].append(word2)
                        word_to_adjacent_words[word2].append(word1)
                
                if different_by_single_letter(word1, begin_word):
                    word_to_adjacent_words[word1].append(begin_word)
                    word_to_adjacent_words[begin_word].append(word1)
        
        connect_words(wordList, beginWord)
        words_to_visit = deque()
        words_to_visit.append((beginWord, 1))
        visited = set()

        while words_to_visit:
            word, distance = words_to_visit.popleft()
            if word == endWord:
                return distance
        
            if word in visited:
                continue

            visited.add(word)
            for adjacent_word in word_to_adjacent_words[word]:
                words_to_visit.append((adjacent_word, distance + 1))
        
        return 0
```
