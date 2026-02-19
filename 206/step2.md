# 他の方の解法やディスコのログを見て解く

## 他の解法
- stack に値ではなくノードのインスタンスを記録して，後ろから繋ぎかえる形でとく
    - https://github.com/irohafternoon/LeetCode/pull/9/changes#diff-f4e1c2e0087bacb60cbc8b5679b00e49b42950124c92b612a64d5bc94e868f73R47

## 参考
- 変数名と引き継ぎのイメージ（1-2 の node, prev の命名について）
    - https://github.com/t0hsumi/leetcode/pull/7/changes#diff-f1530fc1072ee1f0b7de99a2e5236992c72355da69982c8ca516fcfba7c57927R41
        > node は、まだ一番注目しているくらいの意味でいいでしょうが、previous は、意味ではなくて操作の順番から付いている名前ですね。 意味と形式のどちらから名前を付けるかは状況次第で、なるべくパズルを作らないようにつけましょう。
    - これとても整理されている: https://github.com/konnysh/arai60/pull/7/changes#diff-f1530fc1072ee1f0b7de99a2e5236992c72355da69982c8ca516fcfba7c57927R140
        > `previous`, `next`の命名に関して、前後というのは作業者が見ているものに対しての相対的な位置でしかなく、真にその変数が持つ性質を反映したものではない。その変数に何を期待するかを命名に入れた方がいい。`previous`だったら「構築してるノードの先頭」で、`next`は「未処理ノードの先頭」みたいな感じ。そうすればstep1で`previous`を返すという違和感を解消できる。

## 2-1
- stack に node を記録していく
```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
            if head is None:
                return None

            in_order_linked_lists = []
            while head is not None:
                in_order_linked_lists.append(head)
                head = head.next

            reversed_head = in_order_linked_lists.pop()
            reversed_tail = reversed_head

            while in_order_linked_lists:
                reversed_tail.next = in_order_linked_lists.pop()
                reversed_tail = reversed_tail.next
                reversed_tail.next = None
            
            return reversed_head
```

## 2-2
- 1-2 のリファクタ
    - next_node のような変数を最初から設定しなくても，鎖を付け替える前に node.next で next_node の参照をもっておけばいい
    - prev -> reversed_head, node -> in_order_head のようにした
    - 再帰の世界観だと，手作業と対応したある程度納得感のある変数名設定ができるが，ループになると途端にそれができなくなる．
    - 再帰だと，関数の入出力の契約が先に決まっているからやりやすい？てことはループだと変数ごとに期待する状態を設定してあげるといいのかな

```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        in_order_head = head
        reversed_head = None

        while in_order_head is not None:
            next_in_order_head = in_order_head.next
            in_order_head.next = reversed_head
            reversed_head = in_order_head
            in_order_head = next_in_order_head

        return reversed_head
```

## 2-3
- 1-3 のリファクタ
    - 部下から reversed_tail をもらわなくても，head.next で reversed_tail にアクセスできる
    - ref: https://discord.com/channels/1084280443945353267/1231966485610758196/1239417493211320382

```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head
        
        reversed_head = self.reverseList(head.next)
        reversed_tail = head.next
        reversed_tail.next = head
        head.next = None
        return reversed_head
``` 
