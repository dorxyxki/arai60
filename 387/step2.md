# 他の方の解法やディスコのログを見て解く

## 別の解法
- 愚直に初めから文字をみていって，続く文字列に入っているかを見て判定．
    - https://github.com/Hiroto-Iizuka/coding_practice/pull/15/changes#diff-f5212d3af1a56ad98f27100f1fc3ab81096e5caa8fbf5e0e0f78864ad22b24fcR3

- 2回走査する方法
    - 2回目の走査のときに，文字列と位置の情報はとれるので，dict の値に index を保持しておく必要がない
        - https://github.com/kitano-kazuki/leetcode/pull/15/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R13
    - はじめに現れる index を値にして，2回以上みた文字列には -1 を設定する方法も
        -  https://github.com/ksaito0629/leetcode_arai60/pull/14/changes#diff-f5212d3af1a56ad98f27100f1fc3ab81096e5caa8fbf5e0e0f78864ad22b24fcR10

- https://github.com/colorbox/leetcode/pull/29/changes/BASE..48f2749be9c4ec78c6f24c887880e34c7206f678#r1861430039
    - queue をつかって，明示的なFIFOを利用する．1回の走査でいい．

- unique の判定を 左から見つけたときの index と右から見つけた時の index で行う．
    - https://github.com/t0hsumi/leetcode/pull/15/changes#diff-5ec7c3c87171edf4d61e9eb79fd926cafa27caf068da7474222897c8e9e7ab96R197
    - https://github.com/katataku/leetcode/pull/14/changes#diff-5ec7c3c87171edf4d61e9eb79fd926cafa27caf068da7474222897c8e9e7ab96R51-R56
    - O(N^2) かかるが，ネイティブコードなので早い
        - https://github.com/python/cpython/blob/379ab856f59423c570333403a7d5d72f3ea82d52/Objects/stringlib/fastsearch.h#L49-L104
        - 一文字の場合，memchr を利用している

## 参考
- dict の挿入順がたもたれることについて
    - https://docs.python.org/3/library/stdtypes.html#dict
        > Changed in version 3.7: Dictionary order is guaranteed to be insertion order. This behavior was an implementation detail of CPython from 3.6.
    - バージョンで違うなくらい覚えておく

- OrderdDict
    - 順序を保存しておくのに適したHashmap． LRUなどに適している．今回必要な部分に関しては dict で代理できてしまう．
        - https://docs.python.org/3/library/collections.html#ordereddict-objects
            > The OrderedDict algorithm can handle frequent reordering operations better than dict. As shown in the recipes below, this makes it suitable for implementing various kinds of LRU caches.
        - dict との大きな違いはこのあたりか． `od.move_to_end(k, last=False)` みたいに順番を前にもってくることが dict だとできないか．
            > A regular dict does not have an efficient equivalent for OrderedDict’s od.move_to_end(k, last=False) which moves the key and its associated value to the leftmost (first) position.

    - 公式実装
        - https://github.com/python/cpython/blob/c3fb0d9d96902774c08b199dda0479a8d31398a5/Lib/collections/__init__.py#L89
            - doubly linked list + hash map で管理している．
            - self.__map に key: ノード が格納されている．
            - ノードは key, next, prev をもっている．
            - dict の継承で，key: value も持っている．
            - __root や prev を弱参照にする理由はなんだろ, self.__map に責任をもたせたいのかな
            
- LRU
    - lru の再実装
        - https://github.com/t0hsumi/leetcode/pull/15/changes#diff-5ec7c3c87171edf4d61e9eb79fd926cafa27caf068da7474222897c8e9e7ab96R83
        - https://github.com/colorbox/leetcode/pull/29/changes#diff-1df45a8cadd29a30d5c476e5351b11408cd36d95f73255406ff14744932946afR9
        - どちらも dummy の head, tail を利用した実装になっている．

    - 公式実装
        - https://github.com/python/cpython/blob/c3fb0d9d96902774c08b199dda0479a8d31398a5/Lib/functools.py#L604
            - doubly linked list + hash map でかんり
                - こちらだと ノードをリストとして表現してる．
                - ノードは [prev, next, key, value] で情報を持っている
                - Ordered dict のように弱参照などは特に気にしていないみたい．

- dict 
    - pop : https://docs.python.org/3/library/stdtypes.html#dict
        > If key is in the dictionary, remove it and return its value, else return default. 

- next
    - デフォルト値の設定もできる
        - https://docs.python.org/3/library/functions.html#next
            > If default is given, it is returned if the iterator is exhausted.
        - https://github.com/aki235/Arai60/pull/15/changes#diff-9f1474bd47530dcd8167f6047dad22853e10f17c5b71c03f25e28b0835117097R122

- 空間計算量について
    - https://github.com/n6o/leetcode_arai60/pull/15/changes#diff-384d523cbbfefc29f48eeb73d69e164ea4b2c81d6f56a474dad03d70302072d7R26
        > 今回の問題設定ではキーはアルファベット小文字のみ26個
    - たしかに，固定だから O(1) か

## 2-1

- 2回走査でシンプルな書き方

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
                return i
        return -1
```

## 2-2

- 1回の走査
    - dict pop をつかって余分な 条件分岐をなくした & 命名

Time O(N)
Space O(1)

```py
class Solution:
    def firstUniqChar(self, s: str) -> int:
        seen_chars = set()
        unique_char_to_index = dict()

        for i, ch in enumerate(s):
            if ch in seen_chars:
                unique_char_to_index.pop(ch, None)
                continue
            
            unique_char_to_index[ch] = i
            seen_chars.add(ch)
        
        if not unique_char_to_index:
            return -1
        return next(iter(unique_char_to_index.values()))
```
