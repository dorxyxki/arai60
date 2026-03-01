# 他の方の解法やディスコのログを見て解く

## 他の解法
- グループの代表を，{"a": 1, "b": 2} みたいに char: count で表現する
    - hashable なオブジェクトで表現する必要がある．
        - https://github.com/ksaito0629/leetcode_arai60/pull/11/changes#diff-0e9b06f125622811740ec5b217d1d58851ee355271b9ca8f1deab15ad7261df4R54
    - 文字列：頻度を sort して tuple にする選択肢も
        - https://github.com/komdoroid/arai60/pull/2/files#r2554617171
            > 文字列を再構築せずtupleにする
    - もしくは入力がアルファベットの小文字26個という制約を利用し，固定長の配列で頻度のヒストグラムを表現する方法もある．
        - https://github.com/katsukii/leetcode/pull/6/changes#r1899084266
            > ある日、まったく違う事情でここにアルファベットでない文字が流れるようになって、予期せぬ動作をして、原因探しの旅に出てここに行き着くことになります。そのときにどのような動作をこのコードはしていますか。みたいな想像をしています．

- ソートした文字列を代表にする場合
    - ''.join(str) 意外にも，tuple(), str() などでも衝突しないようにできる

## 参考
- ord
    - https://docs.python.org/3/library/functions.html#ord
        - Unicode のコードポイントを返す
    - 文字コードについてわかりやすくまとまっている箇所
        https://github.com/naoto-iwase/leetcode/pull/12/changes#diff-fe2d4c2dc887ab0e20b792b5554f8825a47ae7cb967d7cce8e23a4ea022e660fR133

- dictionary view 
    - https://docs.python.org/3/library/stdtypes.html#dictionary-view-objects
        > They provide a dynamic view on the dictionary’s entries, which means that when the dictionary changes, the view reflects these changes.
        - dict の元の値が変わればかわるのか

- fronzenset
    - https://docs.python.org/3/library/stdtypes.html#frozenset
        > To represent sets of sets, the inner sets must be frozenset objects. 
        - hashable な集合をつくりたいときにつかえる

- 今回使えそうなマジックナンバー用の定数
    - https://docs.python.org/ja/3/library/string.html#string.ascii_lowercase
    - https://github.com/naoto-iwase/leetcode/pull/12/changes#r2416484629

## 2-1
- 文字と頻度でアナグラムを代表する手法

時間計算量 O(NM) 空間計算量 O(NM)

```py
from collections import defaultdict


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        char_count_to_anagrams = defaultdict(list)

        for original_str in strs:
            char_to_count = defaultdict(int)
            for ch in original_str:
                char_to_count[ch] += 1
            
            group_label = frozenset(char_to_count.items())
            char_count_to_anagrams[group_label].append(original_str)
        
        return list(char_count_to_anagrams.values())
```


## 2-2
- 文字と頻度でアナグラムを代表する手法
    - 小文字のアルファべットしか出てこない+コードポイントが連続してるという制約を利用する

時間計算量 O(NM) 空間計算量 O(NM)

```py
from string import ascii_lowercase


class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:

        def count_char_frequency(string_: str) -> tuple:
            char_counter = [0] * len(ascii_lowercase)
            for ch in string_:
                char_pos = ord(ch) - ord("a")
                if not 0 <= char_pos < len(ascii_lowercase):
                    raise ValueError(f"{string_} must be lowercase Englis letter")
                char_counter[char_pos] += 1
            return tuple(char_counter)

        char_count_to_anagrams = dict()
        for original_str in strs:
            char_count = count_char_frequency(original_str)
            char_count_to_anagrams.setdefault(char_count, [])
            char_count_to_anagrams[char_count].append(original_str)
        
        return list(char_count_to_anagrams.values())
```
