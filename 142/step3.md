# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

```py
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:

        def get_junction(head: Optional[ListNode]) -> Optional[ListNode]:
            fast_node = head
            slow_node = head
            while fast_node and fast_node.next:
                fast_node = fast_node.next.next
                slow_node = slow_node.next
                if fast_node is slow_node:
                    return slow_node
            return None
        
        from_junction = get_junction(head)
        from_start = head

        if from_junction is None:
            return None
        else:
            while from_start is not from_junction:
                from_start = from_start.next
                from_junction = from_junction.next
            return from_start
```

```py
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()

        while node:
            if node in visited:
                return node
            
            visited.add(node)
            node = node.next
            
        return None
```
