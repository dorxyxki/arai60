# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

時間計算量 O(Nk) 空間計算量 O(Nk)

```py
class Solution:
    def numUniqueEmails(self, emails: list[str]) -> int:
        unique_emails = set()

        for email in emails:
            local_name, domain_name = email.rsplit("@", maxsplit=1)
            ignored_local_name = local_name.split("+")[0]
            canonicalized_local_name = ignored_local_name.replace(".", "")

            unique_emails.add(f"{canonicalized_local_name}@{domain_name}")
        
        return len(unique_emails)
```
