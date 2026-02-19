# 何も見ずに解く

- 一度ノードを走査し，値を記録．その値をつかってノードを再構築する

時間計算量 O(N) 空間計算量 O(N)

## 1-1
```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        values = []
        node = head
        while node is not None:
            values.append(node.val)
            node = node.next
        
        dummy = ListNode()
        reversed_node = dummy
        while values:
            val = values.pop()
            reversed_node.next = ListNode(val=val)
            reversed_node = reversed_node.next
        
        return dummy.next
```

- 行きがに node の次を張り替えてしまう

時間計算量 O(N) 空間計算量 O(1)

## 1-2
```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None

        prev_node = None
        node = head
        next_node = node.next

        while next_node is not None:
            node.next = prev_node
            prev_node = node
            node = next_node
            next_node = next_node.next
        
        node.next = prev_node
        return node
```

- 再帰
    - N = 5,000 なので python のデフォルトの再帰上限には引っかかりそうだな
    - 部下には head.next を与えて，返ってくるのは head.next 以降が逆になった連結リストの先頭が返ってくる
    - これ部下から先頭だけじゃなくて完成品の尻尾のノードの参照を受け取ったほうがいいのか
    - inner func を新しく作る

時間計算量 O(N) 空間計算量 O(N)

## 1-3
```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return None

        def reverse_list(head: Optional[ListNode]) -> tuple(Optional[ListNode], Optional[ListNode]):
            if head.next is None:
                return head, head
            
            reversed_head, reversed_tail = reverse_list(head.next)
            reversed_tail.next = head
            reversed_tail = head
            reversed_tail.next = None
            return reversed_head, reversed_tail
        
        reversed_head, reversed_tail = reverse_list(head)
        return reversed_head
```
