# 他の方の解法やディスコのログを見て解く


## 他の解法
- 1文字違いの word を探す際
    - word 同士を直接比較すると O(L N^2) かかってしまうので，別の集合 patterns を経由して，隣接する word を探す
        - word (hot) -> patterns (h*t, *ot, ho*) 
            - pattern (h*t) -> words (hot, hit)
            - pattern (*ot) -> words (hot, dot, lot)
            - pattern (ho*) -> words (hot)
        
        - patterns 構築に Time O(NL^2) Space O(NL^2)
            - hash 構築 or キー構築で Time O(L) かかるのか
            - キーを ("h", "t") のようにタプルすることも
                - 入力にワイルドカードが入って来る想定（エスケープするなど）が必要なくなる．
        
        - word -> patterns のエッジはたかだか L 個， pattern -> words のエッジは高々 26 (lowercase englishletter) 個 (wordが被らないなら)
            - つまりグラフのエッジの数が O(NL) になる．

        - BFS において Time O(NL) Space O(N + NL) になる
    
    - word に隣接する可能性のある単語を全て列挙して，wordList に含まれるかを見る．
        - 与えられた wordList の中から条件に合うものを探索するのではなく，全部のありうる隣接ワードを列挙してから絞っていく．発想が逆だが，候補にいれられるといいかも．
            - https://github.com/Yuto729/LeetCode_arai60/pull/25/changes#diff-717004b1fce5b1650ebae5a72e8deee92bf8e56fdb79103e0a064c7c6b66dc68R3
            - https://github.com/kitano-kazuki/leetcode/pull/19/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R213
    
    - 1文字だけ違う2つの文字列は、前半か後半のどちらかが必ず一致することを利用した解法．つまり前半と後半のどちらかが一致したものの中から探索する．
        - https://cs.stackexchange.com/questions/93467/data-structure-or-algorithm-for-quickly-finding-differences-between-strings
            - aaaaaak, aaaaaal など，1バケットに入る要素が大きすぎる場合には，最悪 Time O(N^2k) なので，再帰的に前半と後半を分けて いくことで，最悪 Time O(Nklogk) に改善できる．
            - https://discord.com/channels/1084280443945353267/1200089668901937312/1216123084889788486
                > 頭から半分または尻尾から半分が一致しているはずなので、それでバケットを作ってバケット内でのみ比較すればいいというやりかたもありますね。(編集距離が1であるかの確認に、頭から何文字一致していて、尻尾から何文字一致しているかを足してやればいいという方法をどっかで使ったことあります。)
            - 考え方としては持っておくべきか

        - https://github.com/kitano-kazuki/leetcode/pull/19/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R550
        - https://github.com/ksaito0629/leetcode_arai60/pull/19/changes#diff-02d5c39f8a46d23c396e7bb8fe5f6abba58a78d5971ff34a845f94566d768876R192

- 双方向BFSという手法もあるらしい（後回し）
    - https://github.com/tom4649/Coding/pull/18/changes#diff-32cd28753864a800dd2aeec186c440d8cb812fe6e5d487a54ace66be6ca30d53R1
    - https://github.com/plushn/SWE-Arai60/pull/20/changes#diff-509cdb85d962866b39343fd0a84102c3f1892dbc6f569dba361189bad8f29b37R128


## 参考    
- 遅延評価を利用して，必要な時だけ隣接ワードをみる．探索中に隣接を構築していく
    - https://discord.com/channels/1084280443945353267/1200089668901937312/1215921060411871232
    - 早めに探索が終わるなら，空間計算量を効率化できるのか．

- generator を利用して，隣接ワードを見ていく方法
    - https://discord.com/channels/1084280443945353267/1303605021597761649/1306631474065309728
    - https://github.com/tom4649/Coding/pull/18/changes#diff-1a100bd348e11624e106dc741b5879a174f29de08675c8284dfe1f6b4dbf5c05R4

- BFS の書き方
    - https://github.com/naoto-iwase/leetcode/pull/19/changes#r2427673789
        > 最終的にlayerごとのBFSになっていますが、以下の観点からstep1のようなdequeを使ったBFSのほうが個人的には分かりやすいのではないかと思いました
        > 1. ネストが減らせる
        > 2. countの初期値は0なのか1なのか、returnするのはcountなのかcount+1なのか、またどのタイミングでcountを+1すればいいかなどを悩まなくてよい
        >  りがとうございます。

        > 1. ネストに関してはその通りですね。
        > 2. countについてはむしろ、layer-wiseの方がインクリメントの位置が直感的だと感じました。
        > FIFOを使って一重ループでやると、visitedをboolでなくdistanceで管理すると思いますが、格納するdistanceの計算方法や、実際に入っている値がイメージしづらいなと感じました。
        > 最小距離が同率で複数あってすべて返したい場合や、双方向BFSなども、layer-wiseであれば内側のループに干渉することなく外側のループに条件分岐を追記して書けるのがいいなと思いました。
    
    - 自分はFIFOを使う方が直感的だったが，色々な見方がある．    
    - 確かに layer-wise な書き方の方が，どの層を走査しているかで何か処理を分けたい時などは便利なのか


## 2-1

- とりうる neighbor word を全列挙してから wordList の中を検索する + layer-wise な BFS
    - lower case という限定的な場合に意味がある動作するので，assert で宣言しておくのが良さそう．後々入力が変更されたら意味ない値が出そうなので

Time:  O(NL^2)
Space: O(NL)

```py
from collections import defaultdict
from typing import Iterable
from string import ascii_lowercase as lowercase_letters

class Solution:
    def ladderLength(self, beginWord: str, endWord: str, wordList: List[str]) -> int:        
        word_set = set(wordList)
    
        def get_neighbors(word: str) -> Iterable[str]:
            for i, ch in enumerate(word):
                assert ch in lowercase_letters

                for letter in lowercase_letters:
                    if letter == ch:
                        continue

                    neighbor_word = f"{word[:i]}{letter}{word[i + 1:]}"
                    if neighbor_word in word_set and neighbor_word not in visited:
                        yield neighbor_word

        frontier = [beginWord]
        visited = {beginWord}
        level = 1

        while frontier:
            frontier_neighbors = []
            level += 1

            for word in frontier:
                for neighbor in get_neighbors(word):
                    if neighbor == endWord:
                        return level
                    if neighbor in visited:
                        continue

                    visited.add(neighbor)
                    frontier_neighbors.append(neighbor)

            frontier = frontier_neighbors

        return 0
```

## 2-2

- pattern を経由した文字列の連結

Time:  O(NL^2)
Space: O(NL^2)

```py
from collections import defaultdict
from typing import Iterable

class WordManager:
    def __init__(self, words: list[str]) -> None:
        self._pattern_to_words = defaultdict(list)
        for word in words:
            self.add(word)
    
    def _to_patterns(self, word: str) -> Iterable[tuple]:
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
        visited = {beginWord}
        frontier = [beginWord]
        level = 1
        
        while frontier:
            frontier_neighbors = []
            level += 1
            for word in frontier:
                for neighbor in word_manager.get_neighbors(word):
                    if neighbor == endWord:
                        return level
                    if neighbor in visited:
                        continue

                    frontier_neighbors.append(neighbor)
                    visited.add(neighbor)
            frontier = frontier_neighbors
        return 0
```
