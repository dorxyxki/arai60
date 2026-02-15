# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        unique_head = ListNode()
        unique_tail = unique_head

        node = head

        while node is not None:
            if node.next is not None and node.val == node.next.val:
                duplicated_val = node.val
                while node and node.val == duplicated_val:
                    node = node.next
                continue

            unique_tail.next = node
            unique_tail = node
            node = node.next
            unique_tail.next = None
            
        return unique_head.next
```

## 3-2
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        unique_head = ListNode(val=-float("inf"), next=head)
        node = unique_head

        while node:
            candidate = node.next
            if candidate is None or candidate.next is None or candidate.val == candidate.next.val:
                node = node.next
                continue
            
            val_to_skip = candidate.val
            while candidate and candidate.val == val_to_skip:
                candidate = candidate.next
            node.next = candidate
                
        return unique_head.next
```