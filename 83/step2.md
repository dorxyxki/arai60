# 他の方の解法やディスコのログを見て解く

- 他の解放も視野に入れるべきだった
    - ノードの再構築，その場合空間計算量がO(N): https://github.com/komdoroid/arai60/pull/10/changes
    - setを使った解法，こちらも空間計算量がO(N): https://github.com/fv17/coding-practice/pull/3/changes#r2597582752
    - 再帰でも解ける，スタック分で空間計算量がO(N)に: https://github.com/kitano-kazuki/leetcode/pull/3/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R139
    - if ではなく ループで書くことも可能

- if else ではなく if continue も念頭に: https://github.com/n6o/leetcode_arai60/pull/3/changes#r2751198414

- なにを主役として見ているのか（読み手が読みやすい変数の使用）を考え直す  https://github.com/n6o/leetcode_arai60/pull/3/changes#r2756569698
    > 「順番にノードを確認していって、前のやつと違ったら取っておいて、そうでなかったら捨てる。」が一番単純な表現です。
    - node.next のかわりに node, 無重複の linked list のケツノードを valid_tail みたいな名前で束縛しとくとわかりやすいか
    - ref: https://github.com/appseed246/arai60/pull/4/changes#r2597533498
        > previous, current のように「直前のノード」、「現在のノード」として捉えるのではなく、「重複がないことが確定したノード」と「重複の確認をまだしていないノード」のようにもっと具体的に捉えていくと、より解法の文書化もわかりやすくなるかもしれないです。
        > このコードを見た時に、やりたいことが曖昧なままコードを書いているのではないかと感じました。
    - 確かに，自分のコードを見た時にそう感じる．手作業で思いついた解法をなんとなくコードに落とし込んでいるのがいけない．他の解放も浮かべながらやるのがいいのか

## 2-1
- 「重複がないことが確定したノード」: valid_tail と「重複の確認をまだしていないノード」: node で分けた
- 作業としては node の状態を見て valid_tail を更新していく．
    - 現在の node を捨てる = valid_tail の next を繋ぎかえる
    - 捨てない = valid_tail を node へ
    - node を進める

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head
        
        node = head.next
        valid_tail = head

        while node:
            if node.val == valid_tail.val:
                valid_tail.next = node.next
            else:
                valid_tail = node
            node = node.next

        return head
```

## 2-2 
- 再帰の練習として2-1の手作業を再帰に落とし込んでみたが読みやすくはない（これを再帰で解くといっていいのか？）

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head
        
        def update_valid(valid_tail: Optional[ListNode], node: Optional[ListNode]) -> Optional[ListNode]:
            if node is None:
                return head
            
            if node.val == valid_tail.val:
                valid_tail.next = node.next
                return update_valid(valid_tail, node.next)
            else:
                return update_valid(node, node.next)

        return update_valid(head, head.next)
```

## 再帰
- deleteDuplicatesを再帰的に呼び出す方法を考えたがわからなかった．
- 過去PRあさってみたが，問題の先バレが嫌で再帰のイメージとしてこれくらいしかわからず: 
    - https://github.com/irohafternoon/LeetCode/pull/6/changes/BASE..d6a959670fab5bd6544778035458a895ca2ae5a8#diff-06ed732b43ae8ba90ce66e1356b66c98d9d0238b5338ddf6d80042f58921c3b0R89
        > 要は再帰 = 無限ループ（終了条件を定義して、それ以外は同様の処理を繰り返す）だと理解して、再帰で解くのも自然だと納得した
- 再帰を使用する場合 再帰呼び出しの上限回数に気をつける．（leetcode だと 50,000らしい）: https://github.com/Hiroto-Iizuka/coding_practice/pull/17#discussion_r2716085615
- 参考として 今回ノード数 300 手元の python で 1,000 


- 回答の一例は以下らしいがいっさいわからん

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is not None and head.next is not None:
            if head.val == head.next.val:
                return self.deleteDuplicates(head.next)
            else:
                head.next = self.deleteDuplicates(head.next)
                return head
        return head
```

## 以降整理
- わかりにくいので gpt に解説させる by gpt5.2
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None or head.next is None:
            return head

        head.next = self.deleteDuplicates(head.next)  # 下流に委託

        if head.val == head.next.val:  # 自分の判断（捨てる）
            return head.next
        return head  # 残す
```
- このコードを組み立てる時の気持ち？
- 1. deleteDuplicates(x) を「x から始まる部分リストを、重複なしにして“先頭ノード”を返す」とみなす
- 2. 終端条件を決める．（以降が重複なし確定したら head 返す）
- 3. head.next 以降が重複無しになるように下流の方に処理してもらい，head.next をそれで更新
- 4. 帰ってきたら，今度は上流に渡すための帰りがけ処理として，自分が重複なしリストの先頭になるかを決めて返す．

- 確かにそう考えると再帰でもいけるのか

- 行きがけと帰りがけの処理を考える時
    - 行きがけの時の処理を書くべき時は下流の処理が自分の処理による状態更新の影響を受ける（終端条件とか？）ときか
        - 今回だと，終端条件は端に行ったかどうかだけだから，別に書かなくてもいいのか
    - 今回行きがけを書いてもいい理由は，ノードを捨てる処理は下流の処理に依存しないから．
    - 重複なし先頭ノードを確定する作業は，帰りがけの時しかできない（下流の処理で next 以降が確定した時のみ）

- そう考えると以下は自然に思えるか

```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is not None and head.next is not None:
            if head.val == head.next.val:
                return self.deleteDuplicates(head.next)
            else:
                head.next = self.deleteDuplicates(head.next)
                return head
        return head
```
