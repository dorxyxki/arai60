# 他の方の解法やディスコのログを見て解く

## 別の解法
- stack に スライスではなく左端と右端のインデックスだけを入れる方法
    - https://github.com/rimokem/arai60/pull/24/changes#diff-a8b5de4c7236f863071370937e70e9f97b3bf81e9bf56305bed4f3a03a8db1b4R8
    - https://github.com/Manato110/LeetCode-arai60/pull/24/changes#diff-3485f14f6d9245141e9793341b2e717e06e9ab975483cd5b31de0fdce88d73e1R19

- 子供に作成したノードをくっつける方法
    - https://github.com/arahi10/coding-practice/pull/3/changes#diff-deb4518a80896d8f06ff2fd53d72dbcaf8891d471c02a6298372eb1662a64251R39

- stack に取り出した後に値の設定を、stack には空のTreeNodeを追加する
    - https://github.com/kitano-kazuki/leetcode/pull/24/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R157
    - https://github.com/dxxsxsxkx/leetcode/pull/24/changes#diff-fbc319515fdddece5183edc3b34ae2342a0a66e7e26a3f6a6437d6a654c0fe99R54
    - https://github.com/mamo3gr/arai60/pull/23/changes#diff-42dcab21ce751f482876063d49d9471b46f554a50e46214e677efaaa81c461d7R28
    - 

## 参考
- オーバーフローを防ぐ視点
    - https://github.com/5ky7/arai60/pull/25/changes#r2616702464

- 計算量の話
    - インデックスだけだと
        Time:  O(N)
        Space: O(logN)

        T(N) = 2 * T(N/2) + C
        T(N) = 4 * T(N/4) + 3C
        T(N) = 8 * T(N/8) + 7C
        T(N) = 2^k * T(N/2^k) + 2^k * C
        k <= logN で T(1) = O(1)
        T(N) = O(N) + O(N)
        
        dfs だと 木の高さ分だけスペースを消費するので
        S(N) = O(logN)

    - スライスになると
        Time:  O(NlogN)
        Space: O(N)

        T(N) = 2 * T(N/2) + O(N)
        T(N) = 4 * T(N/4) + 2 * O(N)
        T(N) = 8 * T(N/8) + 3 * O(N)
        T(N) = 2^k * T(N/2^k) + k * O(N)
        k <= logN で T(1) = O(1)
        T(N) = O(N) + O(NogN)

        木の高さkとして
        N + N/2 + N/4 + N/8 + ,,, + 1を積分して 
        S(N) = O(N)


- 同名の変数を避けるよう気をつける
    - https://github.com/nanae772/leetcode-arai60/pull/24/changes#diff-5ee5c2c10d4db77725f47718d23114fe4402e5198494c3a0c20c9692459c616fR10
        > 区間は[begin, end)(beginを含みendを含まない半開区間)で管理するようにした。
    - https://github.com/yumyum116/LeetCode_Arai60/pull/21/changes#r3104903274
        > 配列の範囲としての left / right と、木としての left / right が登場するので、引数名のほうを変えるかなと考えました。その場合 start / end などにするかと思います。
    - https://docs.python.org/3/library/stdtypes.html#range
        - range とかだと、start / stop で stop は含まれないので、そこら辺の命名の感覚を身に付けたい


## 2-1
- インデックスを渡していく手法 + ループでのDFS

Time:  O(N)
Space: O(N)

```py
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None

        root = TreeNode()
        to_build = [(root, 0, len(nums) - 1)]-

        while to_build:
            node, first, last = to_build.pop()
            mid = (first + last) // 2
            node.val = nums[mid]

            if first <= mid - 1:
                node.left = TreeNode()
                to_build.append((node.left, first, mid - 1))
            if last >= mid + 1:
                node.right = TreeNode()
                to_build.append((node.right, mid + 1, last))
            
        return root
```


## 2-2
- 1-2 のりファクア（親に繋げる + dfs）

```py
from enum import auto, Enum


class Direction(Enum):
    left = auto()
    right = auto()


class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        
        dummy = TreeNode()
        to_build = [(0, len(nums) - 1, dummy, Direction.left)]

        while to_build:
            first, last, parent, direction = to_build.pop()
            mid = (first + last) // 2
            node = TreeNode(nums[mid])

            if direction == Direction.left:
                parent.left = node
            else:
                parent.right = node
            
            if first <= mid - 1:
                to_build.append((first, mid - 1, node, Direction.left))
            if mid + 1 <= last:
                to_build.append((mid + 1, last, node, Direction.right))

        return dummy.left
```

## 2-3
- 再帰における帰りがけの接続をループで表現
    - 練習

```py
from enum import auto, Enum


class Direction(Enum):
    down = auto()
    up = auto()


class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None

        result_root = [None]
        stack = [(Direction.down, (0, len(nums) - 1, [None], [None], result_root))]
        while stack:
            direction, args = stack.pop()

            if direction == Direction.down:
                first, last, _, _, ret = args

                if first > last:
                    ret[0] = None
                    continue

                mid = (first + last) // 2
                left_slot = [None]
                right_slot = [None]

                stack.append((Direction.up, (first, last, left_slot, right_slot, ret)))
                stack.append((Direction.down, (first, mid - 1, [None], [None], left_slot)))
                stack.append((Direction.down, (mid + 1, last, [None], [None], right_slot)))

            else:
                first, last, left_ret, right_ret, ret = args
                mid = (first + last) // 2
                
                ret[0] = TreeNode(
                    val=nums[mid],
                    left=left_ret[0],
                    right=right_ret[0],
                )
        
        return result_root[0]
```
