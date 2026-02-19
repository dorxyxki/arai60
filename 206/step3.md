# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed_head = None
        remaining_head = head

        while remaining_head is not None:
            next_destination = remaining_head.next
            remaining_head.next = reversed_head
            reversed_head = remaining_head
            remaining_head = next_destination
        
        return reversed_head
```