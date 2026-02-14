# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
時間計算量 O(N) 空間計算量 O(1)
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head
        
        node = head.next
        valid = head

        while node:
            if node.val == valid.val:
                valid.next = node.next
            else:
                valid = node
            node = node.next
        return head
```

## 3-2
時間計算量 O(N) 空間計算量 O(N)
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head

        node = head.next
        valid = head
        seen_number = set()
        seen_number.add(valid.val)

        while node:
            if node.val in seen_number:
                valid.next = node.next
            else:
                valid = node
                seen_number.add(valid.val)
            node = node.next

        return head
```

## 3-3
時間計算量 O(N) 空間計算量 O(N)
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        head.next = self.deleteDuplicates(head.next)

        if head.val == head.next.val:
            return head.next
        return head
```