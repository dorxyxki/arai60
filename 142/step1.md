# 何も見ずに解く

## 1-1
- 訪問済みノードを set に記録する方法では訪問済みにおいて一致したノードがサイクル開始点がわかる．
時間計算量 O(N) 空間計算量 O(N)

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        visited = set()

        while node:
            if node in visited:
                return node
            visited.add(node)
            node = node.next
        
        return None
```

## 1-2
- 遅いポインタと速いポインタ二つを利用したサイクル検出では 時間計算量 O(N) 空間計算量 O(1) となるので
- これを利用して，サイクル開始地点を探す方法を考える
    - サイクル開始を a, サイクル長を l, 出会い地点を a + b, とおくとそれぞれ移動距離は正の整数 k を利用して
    - 遅いノード: a + b
    - 速いノード: a + b + kl 
    - a + b + kl = 2(a + b)
    - a = kl - b 
    - ここからどうやってaを算出するのかわからず
