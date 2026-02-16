# 何も見ずに解く
2. Add Two Numbers

- 1-1 二つのポインタが指すノードの値を足して，新しい連結リストの値として再構築する．
    - 繰り上げの際は，val=1のノードを作っておく．繰り上げない場合は，完成品の次がないので，val=0で初期化する．
    時間計算量 O(max(N,M)) 空間計算量 O(max(N,M))

## 1-1
```py
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:

        def update_sum_tail(sum_tail:  Optional[ListNode]) ->  Optional[ListNode]:
            if sum_tail.val >= 10:
                sum_tail.val %= 10
                sum_tail.next = ListNode(val=1)
            return sum_tail

        sum_head = ListNode()
        sum_tail = sum_head

        while l1 is not None or l2 is not None:
            if sum_tail.next is None:
                sum_tail.next = ListNode(val=0)            
            sum_tail = sum_tail.next

            if l2 is not None:
                sum_tail.val += l2.val
                l2 = l2.next

            elif l1 is not None:
                sum_tail.val += l1.val
                l1 = l1.next
            else:
                sum_tail.val += (l1.val + l2.val)
                l1 = l1.next
                l2 = l2.next

            sum_tail = update_sum_tail(sum_tail)

        return sum_head.next
```

### あとからみて
- 以下の条件には入らないし，関数の切り出しも必要ない．
```py
            else:
                sum_tail.val += (l1.val + l2.val)
                l1 = l1.next
                l2 = l2.next
```

- 1-2 
    - 注目する連結リスト（nodeが捜査している）と，もう片方の連結リスト（complement が走査）を決めて，node の値に対応する complement の値をたしていく．
    - node が捜査した後ろに sum_tail ができていくイメージ
    - もし complement が途切れたら，繰り上げ処理をしながらそのまま node を進める．
    - もし node が途切れたら，node と complement を入れ替える ＆ sum_tail.next = node としてケツを片方の連結リストに付け替える．
    時間計算量 O(max(N,M)) 空間計算量 O(1)

## 1-2

```py
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        
        def carry_up_node_value(node: Optional[ListNode]) -> Optional[ListNode]:
            if node.val >= 10:
                node.val %= 10
                if node.next is None:
                    node.next = ListNode(val=1)
                else:
                    node.next.val += 1
            
            return node

        node = l1
        complement = l2
        sum_head = ListNode(next=node)
        sum_tail = sum_head

        while complement is not None or node is not None:
            if complement is None:
                node = carry_up_node_value(node)
                sum_tail = node
                node = node.next
                continue
            
            if node is None:
                node, complement = complement, node
                sum_tail.next = node
                sum_tail = node
                node = node.next
                continue
            
            node.val += complement.val
            node = carry_up_node_value(node)
            sum_tail = node
            complement = complement.next
            node = node.next

        return sum_head.next        
```

### あとからみて
- l1 l2 を破壊してしまうような変更をしているが，このモジュールを使う人がどんなことを期待しているのか考える必要があるね
    - https://github.com/aki235/Arai60/pull/5/changes#r2640580945
        - 
