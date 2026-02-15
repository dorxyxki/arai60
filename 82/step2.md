# 他の方の解法やディスコのログを見て解く

- 趣旨としてはどう繋ぎかえるかを考えること: https://github.com/appseed246/arai60/pull/5/changes#r2595241582
    > この問題の作意は、LinkedList の繋ぎ変えをできるかであると思います。

## 他の解法
- 他の解放として 1-1 で使った `max_val` がフラグになっていたり，ループで次まで飛ばす方法があった．
    - ループで重複スキップ: https://github.com/liruly/leetcode/pull/6/changes#diff-905d910d9775bc52269c3e9369a2117492883aa0231266195bf87d256e80e143R65
    - フラグで重複を処理: https://github.com/mamo3gr/arai60/pull/4/changes#diff-d2be5aaee988e58963c8894fe74bba2d04761ed48b60d64456f89c58059a757aR196
- 番兵なしで解く: https://github.com/yus-yus/leetcode/pull/4/changes#diff-ca2358ea3c818999df87750b20d8743e1398d43d373d32a336e1098191bfefbaR147


## 参考
- 番兵について
    - 番兵に使う数字について:
        - 数字自体に意味を推測させない側面も考慮する必要がある: https://github.com/dxxsxsxkx/leetcode/pull/4/changes#r2658899213
        - 将来の入力値の変更をどれくらい想定しているのか: https://github.com/Mike0121/LeetCode/pull/50#discussion_r1966726918
    
- dict について:
    - 挿入順が保たれます: https://docs.python.org/3/library/stdtypes.html#dict
        > Dictionaries preserve insertion order. Note that updating a key does not affect the order. Keys added after deletion are inserted at the end.

- int 型の変数の番兵を初期化する値の話 
    - float("inf")か sys.maxsize を選ぶかのはなし: https://github.com/mamo3gr/arai60/pull/26/changes/BASE..49ac18f645215f540405eee2d322f2cc490e4b70#r2684113100
    - duck-typing とは: 型よりインターフェースを重視する https://docs.python.org/3/glossary.html#term-duck-typing
    - > Integers have unlimited precision.
        https://docs.python.org/3/library/stdtypes.html?utm_source=chatgpt.com#numeric-types-int-float-complex
    - 1-1 だと 比較にしか使われないため，float("inf")でもいいのか
    - そもそも今回にはあまり関係ない

## 2-1
- ループで引き継ぐ時に何を不変条件としているのかを意識して書く
    > 次のやつになる候補は、切断しておいて確定したら繋ぐ方法と、繋いでおいて確定したら切る方法があるでしょう。
    - 個人的にはここら周辺が上記の引用の理解に対してわかりやすかった
    - https://discord.com/channels/1084280443945353267/1227073733844406343/1228598526712483902
    - https://github.com/ichika0615/arai60/pull/5/changes#r1835327666
    - https://github.com/t0hsumi/leetcode/pull/4/changes#r1856455749
    - https://github.com/t0hsumi/leetcode/pull/4/changes#r1868628343

- 不変条件
    - 今見ているノード node より前には node と同じ値を持つノードが存在しないこと
    - unique_tail は完成品の尻尾でそれ以降は何にもつながっていない（切断しておいて確定したら繋ぐ）

時間計算量 O(N) 空間計算量 O(1)

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        unique_head = ListNode()
        unique_tail = unique_head

        node = head

        while node is not None:            
            if node.next is not None and node.val == node.next.val:
                duplicate_number = node.val
                while node is not None and node.val == duplicate_number:
                    node = node.next
            else:
                unique_tail.next = node
                unique_tail = unique_tail.next
                node = node.next

            unique_tail.next = None

        return unique_head.next
```


## 2-2
- 不変条件
    - 今見ているノード node より前には node と同じ値を持つノードが存在しないこと
    - checked までは確定だが，それ以降 ( checked.next 以降)は未確定の物がつながっている．（繋いでおいて確定したら切る）

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        unique_head = ListNode(next=head)
        checked = unique_head

        node = head

        while node is not None:
            if node.next is not None and node.val == node.next.val:
                val_to_remove = node.val
                while node is not None and node.val == val_to_remove:
                    node = node.next
                checked.next = node
                continue
            
            checked = node
            node = node.next
            
        return unique_head.next
```


## 2-3
- 番兵なし, unique_tail なしで書いてみる
- `x is not None` で書くと条件の視認性が悪くなるので `x` でNone判定をする

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        while head:
            if head.next and head.val == head.next.val:
                val_to_remove = head.val
                while head and head.val == val_to_remove:
                    head = head.next
                continue
            else:
                break
            
        node = head
        while node:
            if node.next and node.next.next and node.next.val == node.next.next.val:
                val_to_remove = node.next.val
                while node.next and node.next.val == val_to_remove:
                    node.next = node.next.next
                continue

            node = node.next
            
        return head
```

## 2-3 書き直し
- 条件分岐を自明な変換で書き直したもの
- val_to_remove より val_to_skip でも伝わるか
- ref: https://discord.com/channels/1084280443945353267/1227073733844406343/1228598526712483902

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        while head and head.next and head.val == head.next.val:
            val_to_skip = head.val
            while head and head.val == val_to_skip:
                head = head.next
            
        node = head
        while node:
            if node.next is None or node.next.next is None or node.next.val != node.next.next.val:
                node = node.next
                continue

            val_to_skip = node.next.val
            while node.next and node.next.val == val_to_skip:
                node.next = node.next.next
                        
        return head
```
