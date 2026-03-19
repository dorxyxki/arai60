# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

選んだ理由
- 回答の選択肢として，ありうる文字列を全列挙してから wordList に含まれるか確認する場合，入力の変更に弱い．
- pattern を経由する（tuple による pattern 表現）ことで，より入力の変更に強いものになる．
- BFS は FIFO を明示的に使用する．これは好みかも．

```py
from collections import defaultdict, deque
from typing import Iterable

class WordManager:
    def __init__(self, words: list[str]) -> None:
        self._pattern_to_words = defaultdict(list)
        for word in words:
            self.add(word)

    def _to_patterns(self, word: str) -> Iterable[tuple[str, str]]:
        for i in range(len(word)):
            yield word[:i], word[i + 1:]

    def add(self, word: str) -> None:
        for pattern in self._to_patterns(word):
            self._pattern_to_words[pattern].append(word)

    def get_neighbors(self, word: str) -> Iterable[str]:
        for pattern in self._to_patterns(word):
            for neighbor in self._pattern_to_words[pattern]:
                yield neighbor

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:
        if endWord not in wordList:
            return 0

        word_manager = WordManager([beginWord] + wordList)
        visited = set()
        frontier_and_distance = deque()
        frontier_and_distance.append((beginWord, 1))
        visited.add(beginWord)

        while frontier_and_distance:
            word, distance = frontier_and_distance.popleft()
            if word == endWord:
                return distance            
            for neighbor in word_manager.get_neighbors(word):
                if neighbor not in visited:
                    frontier_and_distance.append((neighbor, level + 1))
                    visited.add(neighbor)
        return 0
```