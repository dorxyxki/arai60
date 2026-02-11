# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        node = head
        visited = set()

        while node:
            if node in visited:
                return True

            visited.add(node)
            node = node.next

        return False
```

## 3-2
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        fast_node = head
        slow_node = head

        while fast_node and fast_node.next:
            fast_node = fast_node.next.next
            slow_node = slow_node.next
            if fast_node is slow_node:
                return True

        return False
```
