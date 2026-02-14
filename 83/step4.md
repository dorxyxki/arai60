# レビュー受けての再帰の書き直し

引き継ぎ方を変えて再帰を書き直す

## 4-1
- 部下に渡す前（引き継ぐ）に自分で重複する値のノードを全てたどってから渡す
- `2,2,2,2,3,4` がきたら `3,4` を部下に渡して `3,4` を受け取り `2,3,4` を上司に返す

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        if head.val == head.next.val:
            while head.next and head.val == head.next.val:
                head = head.next

        deduplicated = self.deleteDuplicates(head.next)
        head.next = deduplicated
        return head
```

## 4-2
- 自分は head と head.next の比較だけして渡す
- `2,2,2,2,3,4` がきたら `2,2,3,4` を部下に渡して `2,3,4` をうけとり `2,3,4` を上司に返す

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        if head.val == head.next.val:
            deduplicated = self.deleteDuplicates(head.next)
            return deduplicated
        else:
            deduplicated = self.deleteDuplicates(head.next)
            head.next = deduplicated
            return head
```

## 4-3
- 4-2 のリファクタ

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        deduplicated = self.deleteDuplicates(head.next)
        
        if head.val == head.next.val:
            return deduplicated
        else:
            head.next = deduplicated
            return head
```
