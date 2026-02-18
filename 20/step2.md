# 他の方の解法やディスコのログを見て解く

## 解き方
- 文字列を置換する手法も考えられる
    - https://github.com/05ryt31/leetcode/pull/7/changes#diff-c9894f5074fc60d98de9a1d8d081cb935197a766771db589ac853c07bcdd328eR19

- 再帰下降

## 参考
- 想定外の入力に対する挙動について
    - https://discord.com/channels/1084280443945353267/1226508154833993788/1227171332131786774
        > 「約束にない間違った使い方をしているのだから、そいつが悪い。驚くような動作をして、デバッグで苦労していても、私の問題ではない。」というのは好まれる態度ではないでしょう。

        > Exception を投げる
        > なんらかの特殊な値を返す
        > プログラムが終了する

    - 契約として括弧以外を受け付けないとすると使用者に宣言できるならば ValueError を返してあげる
        - https://github.com/xbam326/leetcode/pull/8/changes#diff-b20ca50ff3dd72879071fa6d0d86d9e6a0dafc9459ddff7c4c4aa07a646c6c94R29

- シーケンス型の bool 判定について
    - https://google.github.io/styleguide/pyguide.html#2144-decision
        > Use the “implicit” false if possible, e.g., if foo: rather than if foo != []:. There are a few caveats that you should keep in mind though:

- ペアを表す変数命名について
    - open_to_close のような何が返ってくるのかわかりやすい命名も考えられる
        - https://github.com/kunimomo/leetcode/pull/8/changes#r2771430167


下記参考資料（正規文法，有限オートマトン，文脈自由文法，プッシュダウンオートマトンについて）
- オートマトンと形式言語
    https://www.jaist.ac.jp/~uehara/course/2006/ti113/

- https://discord.com/channels/1084280443945353267/1201211204547383386/1202604857664475227
    - > valid parentheses は、チョムスキー階層、タイプ-2、文脈自由文法だから、プッシュダウンオートマトンで書ける、を多分連想します。
    - > これは、全部一つのことを言っていますが、これは常識です。
    - > 正規言語、正規文法、有限オートマトン、と対比されます。


- プッシュダウンオートマトン
    - stack をもった 非決定有限オートマトン
- 再帰下降構文
    - https://github.com/kitano-kazuki/leetcode/pull/6/changes
    - https://github.com/5ky7/arai60/pull/7#discussion_r2067704642
    - https://discord.com/channels/1084280443945353267/1235829049511903273/1238815737548898346


## 2-1
- 変数名の変更
- 想定外の入力に対する処理等

```py
class Solution:
    def isValid(self, s: str) -> bool:
        close_to_open = {
            "}": "{", 
            "]": "[", 
            ")": "("
        }
        open_brackets = []

        for bracket in s:
            if bracket not in close_to_open and bracket not in close_to_open.values():
                raise ValueError("Input must consist of brackets: '{}', '()', '[]'")
            
            if bracket in close_to_open.values():
                open_brackets.append(bracket)
                continue
            
            if not open_brackets:
                return False

            open_bracket = open_brackets.pop()
            if open_bracket != close_to_open[bracket]:
                return False

        return not open_brackets
```

## 2-2
- open to close
```py
class Solution:
    def isValid(self, s: str) -> bool:
        open_brackets = "{[("
        close_brackets = "}])"
        open_to_close = dict(zip(open_brackets, close_brackets))

        invalid_open_brackets = []
        for char in s:
            if char not in open_brackets and char not in close_brackets:
                raise ValueError("Input must consist of brackets: '{}', '()', '[]'")
            if char in open_brackets:
                invalid_open_brackets.append(char)
                continue
            
            if not invalid_open_brackets:
                return False
            
            invalid_open_bracket = invalid_open_brackets.pop()
            if char != open_to_close[invalid_open_bracket]:
                return False
        
        return not invalid_open_brackets
```