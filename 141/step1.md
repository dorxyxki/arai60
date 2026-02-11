# 何も見ずに解く

## 1-1
- 一度走査したノードを後から同一同定できるように set に記録して，一致したものがあればサイクル検出
- 走査したノードを後から同一同定するために id を用いた
- 走査につかう変数名を cursor にした．node はあくまで走査対象で node が動くのは違和感があった．

時間計算量 O(N) 空間計算量 O(N)


```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        cursor = head
        visited_nodes = set()
        
        while cursor:
            visited_nodes.add(id(cursor))
            cursor = cursor.next
            if id(cursor) in visited_nodes:
                return True
        
        return False
```

## 1-2
- 初めて知った時に印象的な手法で覚えていたため，実装（FastとSlowの周回ずれによる検知）

時間計算量 O(N) 空間計算量 O(1)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        fast_cursor = head
        slow_cursor = head

        while slow_cursor and fast_cursor and fast_cursor.next:
            slow_cursor = slow_cursor.next
            fast_cursor = fast_cursor.next.next
            if slow_cursor == fast_cursor:
                return True
        
        return False
```
