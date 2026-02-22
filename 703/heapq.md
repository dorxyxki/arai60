# heapq の再実装

## 1 
- heapify, heappop, heappush の3つの挙動を満たすような実装
- heapify は 葉が minheap として完成していることに注目して，後ろから走査していく．ある値に注目したら，それが子供より大きい限り下に持っていく．そうすると，注目した時点以下の部分木は minheap になる．
- heapppop は知らなかった．末尾 と root（最小値）を入れ替えて，先頭を下ろしていく．
- heappush は新しいものを追加して，それに注目する．それが親より小さい限り，交換して上に持ち上げる．

- 手作業をコードに落とすのが精一杯で，なかなか最初から読む人を意識したコードを上から書いていけない．

```py
class MinHeap:
    def __init__(self, array: list):
        self.binary_min_heap = array
        self.heapify()

    def _swap(self, i: int, j: int):
        self.binary_min_heap[i], self.binary_min_heap[j] = self.binary_min_heap[j], self.binary_min_heap[i]

    def _heapify_subtree(self, i: int):
        while i < len(self.binary_min_heap) // 2:
            min_val, min_id = self.binary_min_heap[i], i
            left_child, left_id = self.binary_min_heap[2*i+1], 2*i+1

            if left_child < min_val:
                min_val, min_id = left_child, left_id

            if 2*i+2 < len(self.binary_min_heap):
                right_child, right_id = self.binary_min_heap[2*i+2], 2*i+2
                if right_child < min_val:
                    min_val, min_id = right_child, right_id
            
            if min_id == i:
                break
            
            self._swap(i, min_id)
            i = min_id

    def heapify(self):
        for i in reversed(range(len(self.binary_min_heap) // 2)):
            self._heapify_subtree(i)

    def heappush(self, val: int):
        self.binary_min_heap.append(val)
        i = len(self.binary_min_heap) - 1
        parent = (i-1)//2
        while parent >= 0:
            if self.binary_min_heap[parent] > self.binary_min_heap[i]:
                self._swap(i, parent)
                i = parent
                parent = (i-1)//2
                continue
            break

    def heappop(self):
        if not self.binary_min_heap:
            raise IndexError()
        
        self._swap(0, -1)
        min_val = self.binary_min_heap.pop()
        if self.binary_min_heap:
            self._heapify_subtree(0)
        return min_val    
```

## 2
- 他人の実装を見てみる
- 多くの方が補助的な操作を切り出しており，ロジックがわかっていると一目で操作のイメージがしやすいコードだと感じたので，同様にかいていく．
- ref:
    - https://github.com/sakupan102/arai60-practice/pull/9/changes
    - https://github.com/python/cpython/blob/31a28cbae0989f57ad01b428c007dade24d9593a/Lib/heapq.py#L132
    - https://github.com/cheeseNA/leetcode/pull/12/changes
    - https://discord.com/channels/1084280443945353267/1192736784354918470/1194613857046503444

- cpython: https://github.com/python/cpython/blob/31a28cbae0989f57ad01b428c007dade24d9593a/Lib/heapq.py#L132
    - _shiftdown が heappush の内部実装に対応しているのか
    - _shiftupは，_heapify_subtree ににているが
        - 一旦注目しているノードを小さい子供の方を選択しながら一番下まで交換してあげる．
        - そのあと _shiftdown を再利用して，上に持ち上げる．
        - なんで？
        - 理由
            - _shiftup は heappop で呼ばれてる．
            - heappop するときに，root にもってくる末尾の値が大きくなりがちで，_shiftup のみで早く抜けることはほぼなく，毎回その分比較が必要に．
            - 加えて，比較のコストが高い可能性がある．
            - 比較の回数をへらせる実装にしたいから．
                - https://github.com/python/cpython/blob/31a28cbae0989f57ad01b428c007dade24d9593a/Lib/heapq.py#L221-L258
    - 注目している値が木の下にいくのに _shiftup, 木の上に行くのに _shiftdown の名前というずれがあるので，どちらかというとコードを書いた人は注目している値ではなくて，その交換対象がどう振る舞うかを見ているんだろうな

```py
class MinHeap:
    """
    Transform an exsisting list into an min-heap using heapify, heappush, 
    """
    def __init__(self, array: list):
        self.min_heap = array
        self.heapify()

    def _swap(self, i: int, j: int):
        self.min_heap[i], self.min_heap[j] = self.min_heap[j], self.min_heap[i]

    def _left_child(self, index: int):
        return 2 * index + 1

    def _right_child(self, index: int) -> int:
        return 2 * index + 2

    def _has_left_child(self, index: int) -> bool:
        return self._left_child(index) < len(self.min_heap)

    def _has_right_child(self, index: int) -> bool:
        return self._right_child(index) < len(self.min_heap)
    
    def _has_child(self, index: int) -> bool:
        return self._has_left_child(index)
    
    def _smaller_child(self, index: int) -> int:
        smaller_index = self._left_child(index)
        if (
            self._has_right_child(index)
            and self.min_heap[self._right_child(index)] < self.min_heap[self._left_child(index)]
        ):
            smaller_index = self._right_child(index)
        return smaller_index

    def _is_valid_relation(self, parent: int, small_child: int) -> bool:
        return self.min_heap[parent] <= self.min_heap[small_child]
    
    def _parent(self, index: int) -> int:
        return (index - 1) // 2
    
    def _has_parent(self, index: int) -> bool:
        return self._parent(index) >= 0

    def _heapify_subtree(self, index: int):
        while self._has_child(index):
            small_child_index = self._smaller_child(index)
            if self._is_valid_relation(index, small_child_index):
                break

            self._swap(index, small_child_index)
            index = small_child_index

    def heapify(self):
        for i in reversed(range(len(self.min_heap) // 2)):
            self._heapify_subtree(i)

    def heappop(self) -> int:
        if not self.min_heap:
            raise IndexError()

        self._swap(0, -1)
        smallest_value = self.min_heap.pop()
        if self.min_heap:
            self._heapify_subtree(0)

        return smallest_value
    
    def heappush(self, value: int):
        self.min_heap.append(value)
        index = len(self.min_heap) - 1
        while self._has_parent(index): 
            parent_index = self._parent(index)
            if self._is_valid_relation(parent_index, index):
                break
            
            self._swap(index, parent_index)
            index = parent_index
```