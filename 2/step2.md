# 他の方の解法やディスコのログを見て解く

## 他のアプローチ
- 連結リスト-> int -> 足し算 -> 連結リスト再構築
    - C++ では 100 桁の整数が表現できない https://github.com/5103246/LeetCode_Arai60/pull/5/changes/475690037f777b12e6f01dfd3335fd7c2fbcb5db#diff-a98fc3b0b46e654144d4c97be1242fc3809afec6094e724777d69d59c34ad9a1R9
    - log2 = 0.301 くらいなので 2**64 でも 20桁くらい
    - pythonだと int の精度が 無制限だからできるのか
        ```
        >>> A = int("1" + ("0" * 10000))
        Traceback (most recent call last):
        File "<stdin>", line 1, in <module>
            A = int("1" + ("0" * 10000))
        ValueError: Exceeds the limit (4300 digits) for integer string conversion: value has 10001 digits; use sys.set_int_max_str_digits() to increase the limit
        >>> 
        >>> A = int("1" + ("0" * 1000))
        >>> A
        10000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
        ```

- 繰り上がり状態を管理する変数を用いて解く
    - while のなかで繰り上がりも条件に加えることでさらにすっきりとした書き方になるのか
    - https://github.com/ksaito0629/leetcode_arai60/pull/1/changes#diff-5eb9a962eebf3ee7258012a56131258b65ca507f0240cfc7cb5388670ce356f5R1-R166

- 再帰で解く方法
    - https://github.com/5103246/LeetCode_Arai60/pull/5/changes/475690037f777b12e6f01dfd3335fd7c2fbcb5db#diff-a98fc3b0b46e654144d4c97be1242fc3809afec6094e724777d69d59c34ad9a1R90
    - https://github.com/n6o/leetcode_arai60/pull/5/changes#diff-d01eb388651b2155d96cc3dd3187971eab0516cf1c8e78d419bdda15d17c2ad5R151

- 番兵を使わない方法
    - https://github.com/kazukiii/leetcode/pull/6#discussion_r1633605853


### memo
- `carry, val = divmod(sum_val)` みたいな書き方も可能
- divmod
    https://docs.python.org/ja/3/library/functions.html#divmod

- python における `%`, `//` の挙動について
    - `//`: 
        - https://docs.python.org/ja/3/library/stdtypes.html
            > 結果は常に負の無限大の方向に丸められます: 1//2 は 0 、 (-1)//2 は -1 、 1//(-2) は -1 、そして (-1)//(-2) は 0 です。
    - `%`: 
        - https://docs.python.org/ja/3/reference/expressions.html#binary-arithmetic-operations 
            > 切り捨て除算演算と剰余演算は、恒等式: x == (x//y)*y + (x%y) の関係にあります。
        ```
        >>> 13 // 10
        1
        >>> 13 // -10
        -2
        >>> -13 // -10
        1
        >>> -13 // 10
        -2
        >>> -13 % 10
        7
        >>> 13 % -10
        -7
        ```
    - https://discord.com/channels/1084280443945353267/1084283898617417748/1199292102161481778
        > 他の言い方をすると、割り算を使ったことがないならば仕方がないんですが、使ったことがあるならば、
        > ・0で割ったらどうなる?
        > ・負の数を突っ込んだら何が起きる?
        > は疑問に持ち、公式マニュアル等で調べているんじゃないの? というのが疑問なわけです。
    - java の挙動: 
        https://github.com/appseed246/arai60/pull/6/changes#diff-f8c6193395f9388d17dd24e1077ee3fd945af97c007974eaa2fda8d9e1634606R32

    ```

    ```

## 2-1
- 繰り上がり変数を状態として引き継ぐことを考えるととても自然にかけた．
- 1-1 でも繰り上がりを処理することは考えたが，1-1 と比べるととても整理されている．
    - 引き継ぎ情報をノードの状態に埋め込むのか，それとも変数に入れるのかの違いか

時間計算量 O(max(N,M)) 空間計算量 O(max(N,M))
```py
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        sum_head = ListNode()
        sum_tail = sum_head

        carry = 0
        while l1 is not None or l2 is not None or carry != 0:
            sum_val = carry

            if l1 is not None:
                sum_val += l1.val
                l1 = l1.next
            
            if l2 is not None:
                sum_val += l2.val
                l2 = l2.next

            carry = sum_val // 10
            sum_val %= 10                        
            sum_tail.next = ListNode(val=sum_val)
            sum_tail = sum_tail.next

        return sum_head.next
```
## 2-2
- 番兵を使わない方法

時間計算量 O(max(N,M)) 空間計算量 O(max(N,M))
```py
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        sum_head = None
        node = None
        carry = 0

        while l1 is not None or l2 is not None carry != 0:
            sum_val = carry
            if l1 is not None:
                sum_val += l1.val
                l1 = l1.next
            if l2 is not None:
                sum_val += l2.val
                l2 = l2.next
            
            carry = sum_val // 10
            sum_val %= 10
            if sum_head is not None:
                sum_head = ListNode(val=sum_val)
                node = sum_head
                is_lowest = False
                continue
            
            node.next = ListNode(val=sum_val)
            node = node.next

        return sum_head
```

## 2-3
- 再帰を使って解いてみる
    - 番兵なし
    - 上司からは 繰り上げるかどうか，あとは担当するノードたちがくる
    - 部下に同様の情報をあたえてやると，自分が作った node の next に繋がる答えの連結リストを返してくれることを期待する．

時間計算量 O(max(N,M)) 空間計算量 O(max(N,M))
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
            sum_val %= 10
            node = ListNode(val=sum_val)
            node.next = add_two_numbers(l1, l2, carry)
            return node
        
        return add_two_numbers(l1, l2, 0)
```

## 2-4
- 再帰を使って解いてみる
    - 番兵あり
    - https://discord.com/channels/1084280443945353267/1235829049511903273/1238142937028890634
    - https://github.com/tNita/arai60/pull/6/changes#diff-f239586f007e3fadc767a5ef76b07232f6dc67b3379a66bc83899d3872666df4R116
    - 番兵を設定すると行きがけで全て完了させることができて帰りはいらないのか
    - 自分の仕事は sum_tail までの完成品を作ってわたす（一番下の部下まで行ったらおわり）

```py
class Solution:
    def add_two_numbers(self, l1: Optional[ListNode], l2: Optional[ListNode], sum_tail:Optional[ListNode], carry: int) -> Optional[ListNode]:
        if l1 is None and l2 is None and carry == 0:
            return None
        
        l1_val = 0
        if l1 is not None:
            l1_val += l1.val
            l1 = l1.next

        l2_val = 0
        if l2 is not None:
            l2_val += l2.val
            l2 = l2.next

        sum_val = l1_val + l2_val + carry
        carry, remainder = divmod(sum_val, 10)
        sum_tail.next = ListNode(remainder)
        self.add_two_numbers(l1, l2, sum_tail.next, carry)

    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        sum_tail = ListNode()
        self.add_two_numbers(l1, l2, sum_tail, 0)
        return sum_tail.next
```