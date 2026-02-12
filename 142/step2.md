# 他の方の解法やディスコのログを見て解く

## 2-1
- two-pointerの解法: 出会った後，片方を head に戻し，両者1ステップずつ進めて出会った地点が開始地点になる．
    - step1から足りなかったのは
    - a = kl - b = (k-1)l + l-b
    - 上記のよう見ると head に戻した ptr が a に着くまでに head に戻さなかった ptr が k-1 回サイクルを周回し，l-b 進むとちょうどサイクル開始点にくるから開始地点がわかるのか．

- 手法を理解して実装
- while のなかに while がネストされるのがぱっと見わかりにくそうだったのでフラグ変数を介した

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast_node = head
        slow_node = head
        has_cycle = False

        while fast_node and fast_node.next:
            fast_node = fast_node.next.next
            slow_node = slow_node.next
            if fast_node is slow_node:
                has_cycle = True
                break
            
        if has_cycle:
            node = head
            while node is not slow_node:
                node = node.next
                slow_node = slow_node.next
            return node
        else:
            return None
```


- フラグ変数をなしで書いてみる
- ref: https://discord.com/channels/1084280443945353267/1221030192609493053/1225674901445283860

## 2-2
- サイクル検出を関数化した
- 出会うかどうかの検出処理と出会ってからサイクル開始検出が分離されており見やすい
- get_met_node とかより get_junction get_confluence の方がいいかもしれない．

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:

        def get_met_node(head: Optional[ListNode]) -> Optional[ListNode]:
            fast_node = head
            slow_node = head
            while fast_node and fast_node.next:
                fast_node = fast_node.next.next
                slow_node = slow_node.next
                if fast_node is slow_node:
                    return slow_node
            else:
                return None
        
        met_node = get_met_node(head)

        if met_node is None:
            return None
        else:
            node = head
            while node is not met_node:
                node = node.next
                met_node = met_node.next
            return node
```


## 2-3
- 無限ループ
- while でなにをしているのか見やすくなったきがする
- いろんな意見がある https://github.com/tNita/arai60/pull/3/changes#r2555247077
- node ではなく from_meeting_point とかもわかりやすい

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:        
        fast_node = head
        slow_node = head
        while True:
            if fast_node is None or fast_node.next is None:
                return None
            
            fast_node = fast_node.next.next
            slow_node = slow_node.next
            if fast_node is slow_node:
                break
        
        node = head
        while node is not slow_node:
            node = node.next
            slow_node = slow_node.next
        return node
```

## 2-4
- while else をつかう
- あまり使ったことがなく，いつ抜けてどう処理するのかわかりにくい．
- https://github.com/ryomappi/Arai60/pull/2/changes#r2714923517

```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast_node = head
        slow_node = head
 
        while fast_node and fast_node.next:
            fast_node = fast_node.next.next
            slow_node = slow_node.next
            if fast_node is slow_node:
                break
        else:
            return None

        if fast_node is slow_node:
            node = head
            while node is not slow_node:
                node = node.next
                slow_node = slow_node.next
            return node
```
