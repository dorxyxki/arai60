# 何も見ずに解く

## 1-1
- 対象が sorted linked list なので愚直に前から見ていって 次のノードの値 が増加しているなら次，していないなら次のノード先をさらに次のノードにする．
時間計算量 O(N) 空間計算量 O(1)
```py
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        while node and node.next:
            if node.next.val > node.val:
                node = node.next
            else:
                node.next = node.next.next
        return head
```

## 1-2
- `while node and node.next` の node は最初の head is None の時にしか機能しないので，はやめに返してあげることで node.next だけに注目しており読みやすくなる気がした．
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head

        node = head
        while node.next:
            if node.next.val > node.val:
                node = node.next
            else:
                node.next = node.next.next
        return head
```