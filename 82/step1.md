# 何も見ずに解く

- 思いついた解法
    - 1-1 走査するノード(node)と次のノード(node.next)の比較をすすめる．値が一致していた場合に保存しておき，その値を元にユニークなノードを繋ぎ直す．最初からダブっている場合に対して番兵を用意する．
        時間計算量 O(N) 空間計算量 O(1)
    - 1-2 再帰を用いる方法: 
        時間計算量 O(N) 空間計算量 O(N)
    - 一度完全に Linked List を走査して値と出てきた回数を dict に記録し，1回出現のノードのみで再構築する
        - dict が key の順番を挿入順に保ってくれている場合（要調査）
            時間計算量 O(N) 空間計算量 O(N)
        - されていない場合
            時間計算量 O(Nlog(N)) 空間計算量 O(N)

## 1-1 (不正解)
- エッジケースでNG [1, 1] -> [1] となる．
- 最後がユニークでない数字で終わる場合，それを弾けない．

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head

        valid_head = ListNode(next=head)
        valid = valid_head
        max_val = -float("inf")

        node = head

        while node.next is not None:
            if node.val == node.next.val or node.val == max_val:
                max_val = node.val
                valid.next = node.next
            else:
                valid.next = node
                valid = valid.next

            node = node.next

        return valid_head.next
```

最後の一行を以下のように書けば，エッジケースに対応できそうだが，そもそも解き方をかえたり，注目している変数を変えた方が読みやすく書けそう

```py
        if node.val == max_val:
            valid.next = node.next

        return valid_head.next
```

## 1-2
- 自分の仕事: head と head.next の値が等しい場合，等しくないノードが出てくるまで head をずらし，部下に渡し，帰ってきた完成品をそのまま返す．等しくない場合，部下から帰ってきた完成品を head にくっつけて返す．
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        if head.val == head.next.val:
            while head.next is not None and head.val == head.next.val:
                head = head.next
            
            unique = self.deleteDuplicates(head.next)
            return unique
        
        unique = self.deleteDuplicates(head.next)
        head.next = unique
        return head
```