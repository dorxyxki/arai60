# 何も見ずに解く

Constraints:

1 <= emails.length = N <= 100
1 <= emails[i].length = k <= 100

## 考えたこと
- 正規化した email をキーにして unique emails の集合を管理する．
    - str のメソッドを使うとスッキリかけるけど，前から一文字ずつみていってもできそう．

- エラー処理はこれを呼び出す側に書いてもらうのがいいのかな，どちらもやろうとするとロジックが不透明になるんじゃないかな
    - 読んでもらう側に弾いてもらうことを明示するべきか？

## 1-1

時間計算量 O(Nk) 空間計算量 O(Nk)

```py
class Solution:
    def numUniqueEmails(self, emails: list[str]) -> int:
        
        def split_and_convert_local_name(email: str) -> tuple[str, str]:
            local_name = ""
            i = 0

            while i < len(email) and email[i] != "@":
                if email[i] == "+":
                    while i < len(email) and email[i] != "@":
                        i += 1                
                    break

                if email[i] == ".":
                    i += 1
                    continue

                local_name += email[i]
                i += 1

            domain_name = email[i + 1:]
            return local_name, domain_name

        unique_emails = set()
        for email in emails:
            unique_email = split_and_convert_local_name(email)
            if unique_email not in unique_emails:            
                unique_emails.add(unique_email)

        return len(unique_emails)
```

文字列の再構成を行っているので，
時間計算量 O(N*k^2) 空間計算量 O(Nk)
