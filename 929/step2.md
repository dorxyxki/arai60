# 他の方の解法やディスコのログを見て解く

## 他の解法
- 一文字ずつ処理する（ステートマシン）
    - https://discord.com/channels/1084280443945353267/1200089668901937312/1207996784211918899
    - https://github.com/kitano-kazuki/leetcode/pull/14/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R50

- 正規表現
    - https://github.com/naoto-iwase/leetcode/pull/14/changes#diff-3282ee8d1849a45b92b2a4a6e00440ecc86e0ae920337bcef2ea279c2d29b848R125
    - https://github.com/X-XsleepZzz/leetcode/pull/15/changes#diff-d65d43698547a0f3cfcdb7f005de30ed4cd0c45ae015fd01094d6647cfa0a84aR108
    - https://github.com/olsen-blue/Arai60/pull/14/changes#diff-d65d43698547a0f3cfcdb7f005de30ed4cd0c45ae015fd01094d6647cfa0a84aR54
    - Email validation
        - https://www.regular-expressions.info/email.html
    
## 参考
- この問題を見た時に欲しい視野と常識について
    - https://github.com/akmhmgc/arai60/pull/11#discussion_r2311995496

- 文字列の再構築
    - Immutable を再構築する場合，O(N^2) かかるので，リストから再構築するのが進められる．
        - 今回でいうと O(k^2) かかる．
            - Email の規格で最大254 文字として，
            - K = 100，N=10^6なら， Cpythonが 10^7 クロック/ s 処理できるとしても全部で 1000 s かかる．''.join() なら 10 s
    - https://docs.python.org/3/library/stdtypes.html#common-sequence-operations
        > Concatenating immutable sequences always results in a new object. This means that building up a sequence by repeated concatenation will have a quadratic runtime cost in the total sequence length. To get a linear runtime cost, you must switch to one of the alternatives below:
        > if concatenating str objects, you can build a list and use str.join() at the end or else write to an io.StringIO instance and retrieve its value when complete

- RFC
    - RFCはそもそも規格ではなく仕様らしい
        - https://github.com/rinost081/LeetCode/pull/13#discussion_r2099861018
    
    - email address の制約
        - https://datatracker.ietf.org/doc/html/rfc5322#section-3.4.1
        - https://datatracker.ietf.org/doc/html/rfc5322#section-3.2.4
            > Strings of characters that include characters other than those allowed in atoms can be represented in a quoted string format, where the characters are surrounded by quote (DQUOTE, ASCII value 34) characters.
        - https://github.com/plushn/SWE-Arai60/pull/14#discussion_r2051704322
    
    - 補足：ABNF記法をよんだらある程度理解できるようになった．
        - https://datatracker.ietf.org/doc/html/rfc5234

- アドレスの長さ制限
    - 254 文字が限界らしい
    - https://github.com/plushn/SWE-Arai60/pull/14#discussion_r2052171339
    - https://stackoverflow.com/questions/386294/what-is-the-maximum-length-of-a-valid-email-address
    
- ユースケースについての考え
    - https://github.com/achotto/arai60/pull/4/changes#r2776481399
        > つまり、たとえば、誤った入力が一つ入ったときに、全体として、そこそこ動いて欲しいのか、異常だといって止まって欲しいのか。それは何をしたいのかとの兼ね合いになるでしょう。

- python の組み込み
    - rpartition
        - https://docs.python.org/ja/3/library/stdtypes.html#str.partition
    - rsplit
        - https://docs.python.org/3/library/stdtypes.html#str.rsplit
            > If maxsplit is given, at most maxsplit splits are done, the rightmost ones.
    - endswith
        - https://docs.python.org/3/library/stdtypes.html#str.endswith
    - re
        一旦さらっとみて正規表現になれる
        - re.sub
            - https://docs.python.org/ja/3/library/re.html#re.sub
        - 他にも fullmatch, findall, match, search などがあるらしい．
            ```py
            re.match("c", "abcdef")    # No match
            re.search("c", "abcdef")   # Match
            re.fullmatch("p.*n", "python") # Match
            re.fullmatch("y.*n", "python") # No match
            ```
- try-except について
    - https://github.com/mamo3gr/arai60/pull/14/changes#r2650544731
        > あくまで個人的な感覚ですが、 try except は重そうという印象があります。実際にどの程度重いかは実装してみないと何とも言えません。個人的には、エラーコード等で代替するなど、現実的なコストで避けられるのであれば避けたいです。

- 型ヒントの利用
    - https://github.com/Hiroto-Iizuka/coding_practice/pull/14/changes#r2670839383
        > 実際には (local, domain) を入れるので、unique(_local_domain)_pairs のように名前に表すか、型ヒントがあると親切に思いました。


## 2-1
- 大量の人に対してメールを送るケースを考えると，有効でないメールが来ても，いちいち落ちないで欲しい．
    - `_try_canonicalize` で invalid なメールが来たら None を返すようにして対応
    - 書いてみて思ったが，可読性低い

時間計算量 O(Nk) 空間計算量 O(Nk)

```py
class Solution:
    def _is_valid_domain(self, domain: str) -> bool:
        len_dot_com = len(".com")
        if len(domain) < len_dot_com or not domain.endswith(".com") or domain[:-len_dot_com] == "":
            return False
        return True

    def _is_valid_char(self, ch: str) -> bool:
        if ch == "." or ch == "+" or ord("a") <= ord(ch) <= ord("z"):
            return True
        return False

    def _try_canonicalize(self, email: str) -> str | None:
        email = email.rsplit("@", maxsplit=1)
        if len(email) == 1:
            return None
        
        local_part, domain_part = email
        if not self._is_valid_domain(domain_part):
            return None

        is_behind_plus = False
        canonicalized_local_part = []
        
        for ch in local_part:
            if not self._is_valid_char(ch):
                return None

            if is_behind_plus or ch == ".":
                continue

            if ch == "+":
                is_behind_plus = True
                continue  

            canonicalized_local_part.append(ch)
        
        if not canonicalized_local_part:
            return None

        canonicalized_local_part = "".join(canonicalized_local_part)
        return f"{canonicalized_local_part}@{domain_part}"

    def numUniqueEmails(self, emails: list[str]) -> int:
        unique_emails = set()
        for email in emails:
            unique_email = self._try_canonicalize(email)
            if unique_email is not None:
                unique_emails.add(unique_email)

        return len(unique_emails)
```

## 2-2
- 組み込みメソッドを利用する

時間計算量 O(Nk) 空間計算量 O(Nk)

```py
class Solution:
    def numUniqueEmails(self, emails: list[str]) -> int:
        unique_emails = set()

        for email in emails:
            local_part, domain_part = email.rsplit("@", maxsplit=1)
            local_part_ahead_plus = local_part.split("+", maxsplit=1)[0]
            canonicalized_local_part = local_part_ahead_plus.replace(".", "")

            unique_email = f"{canonicalized_local_part}@{domain_part}"
            unique_emails.add(unique_email)
        
        return len(unique_emails)
```
