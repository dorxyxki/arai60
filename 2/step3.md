# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

```py
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        
        dummy = ListNode()
        sum_tail = dummy
        carry = 0

        while l1 is not None or l2 is not None or carry != 0:
            sum_value = carry

            if l1 is not None: sum_value += l1.val
            if l2 is not None: sum_value += l2.val

            sum_tail.next = ListNode(sum_value%10)
            carry = sum_value // 10
            
            sum_tail = sum_tail.next
            if l1 is not None: l1 = l1.next
            if l2 is not None: l2 = l2.next
        
        return dummy.next
```

## 3-2

```py
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        
        head = None
        node = None
        carry = 0

        while l1 is not None or l2 is not None or carry != 0:
            l1_val = 0
            if l1 is not None:
                l1_val = l1.val
                l1 = l1.next
            l2_val = 0
            if l2 is not None:
                l2_val = l2.val
                l2 = l2.next
            
            sum_val = l1_val + l2_val + carry
            carry = sum_val // 10

            if head is None:
                head = ListNode(sum_val % 10)
                node = head
            else:
                node.next = ListNode(sum_val % 10)
                node = node.next
        
        return head            
```

## 3-3

```py
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        
        def add_two_numbers(l1: Optional[ListNode], l2: Optional[ListNode], carry: int) -> Optional[ListNode]:
            if l1 is None and l2 is None and carry == 0:
                return None
            
            sum_val = carry
            if l1 is not None:
                sum_val += l1.val
                l1 = l1.next

            if l2 is not None:
                sum_val += l2.val
                l2 = l2.next
            
            carry = sum_val // 10
            sum_node = ListNode(sum_val % 10)
            sum_node.next = add_two_numbers(l1, l2, carry)
            return sum_node
        
        return add_two_numbers(l1, l2, 0)
```
