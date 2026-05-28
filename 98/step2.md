# 他の方の解法やディスコのログを見て解く

## 別の解法
- 上から探索して行ったときに、ノードの値の範囲が決まることを使用する方法
    - https://github.com/mamo3gr/arai60/pull/26/changes#diff-76f9ae02b0354cd8190a5e54a28f2b1bf00fb51b71f4b488a3cf1ff63d032f9bR10
    
    - preorder

- inorder で探索しながら値が単調増加になっているかの確認をする
    - BSTの特性に沿った解法
    
    - https://github.com/h-masder/Arai60/pull/31/changes#diff-f0cc16723b94db1c198faa44635c280381a359a80ce0d8dc401be849d2f1f757R27
    - https://github.com/tom4649/Coding/pull/27/changes#diff-b1ced9968236c5aadaa90e141021402b0739eec3776766fb18f130a9c431dcf6R1-R32
    - https://github.com/Manato110/LeetCode-arai60/pull/28/changes#diff-e50853762a69b0803fef165889f1ab2d17f57f8d93d51353dfe6345d2f20b379R150
    
    - generator を使った解法も
        - https://github.com/ksaito0629/leetcode_arai60/pull/20/changes#diff-fc5817f258dba5c583e3ba5ce9c4eb8a2277d982a969496dfbdc2aeb29c88404R69
        - https://github.com/nittoco/leetcode/pull/35/changes#r1739978684
            > pros cons の cons としては、再帰の深さの限界があること、走りかけの Generator を木の深さ分だけ作るのでそこそこ重いことがありそうですね。

- 左の部分木の最大値 < node < 右の部分木の判定をする。この際、最大、最小を葉から伝搬させていく
    - 葉から最大最小を伝搬させていくようにすると、 同じノードを2回走査する必要はない。

    - 再帰
        - https://github.com/atmaxstar/coding_practice/pull/7/changes#diff-5011720fdb4fd01a6fdb6652dbfad5c40b3354846103ed874aba9bdcd7a0ec93R12
    
    - stack iterative
        - https://github.com/naoto-iwase/leetcode/pull/33/changes/BASE..2404c21c24a749b6f871d9030b7df0096beb856d#r2479195403
        - https://github.com/naoto-iwase/leetcode/pull/33/changes/BASE..2404c21c24a749b6f871d9030b7df0096beb856d#diff-9fdcc2342d5e1d4a23edd1bd1fd71af2b84024472dec032836dcd610e914dd0fR322
            - 二回走査の書き方もある
            - root -> right -> left で作り、末尾から取り出すことで left -> right -> root を実現しているのか
    
    - postorder に対応する


## 参考
- 境界の変数名について、
    - https://github.com/dxxsxsxkx/leetcode/pull/28#discussion_r2752202157
        > 私だけかもしれませんが、min_value, max_valueがなんの最小値・最大値なのか読み取るのに苦労しました。部分木の最小・最大値でもありませんし、とってもいい値の最小・最大でもない (とっても良い値は min_value < x < max_value : exclusive) なので、やや語弊があるかなと思います。
    - https://github.com/kitano-kazuki/leetcode/pull/28/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R235
        - 確かに upper_bound_exclusive などは良さそう

- root is Noneの時に何を返すか
    - https://github.com/h-masder/Arai60/pull/31/changes#diff-e6d5ee0fe126c5c2084782d9f1d51f1328c22022bcab8102bc5c415c1e0d8310R28
    - 空の木と見ると、BSTの条件に違反していないのでTrueを返すのが自然なのか

- https://docs.python.org/3/reference/expressions.html#grammar-token-python-grammar-yield_atom
    - yield from でイテレータを受け取れる

## 2-1
- preorder iterative

```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if root is None:
            return True

        node_with_bounds = [(root, float("-inf"), float("inf"))]
        while node_with_bounds:
            node, strict_lower, strict_upper = node_with_bounds.pop()
            if not (strict_lower < node.val < strict_upper):
                return False

            if node.left is not None:
                node_with_bounds.append((node.left, strict_lower, node.val))
            if node.right is not None:
                node_with_bounds.append((node.right, node.val, strict_upper))
        
        return True
```

## 2-2
- inorder iterative

```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def push_left_path(node: Optional[TreeNode]):
            while node is not None:
                to_visit.append(node)
                node = node.left

        to_visit = []
        min_value = float("-inf")
        
        push_left_path(root)
        while to_visit:
            node = to_visit.pop()
            if not min_value < node.val:
              return False  

            min_value = node.val
            if node.right is not None:
                push_left_path(node.right)
        
        return True
```


## 2-3
- inorder recursive
    - generatorを使う方法

```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:        
        def fetch_inorder(node: Optional[TreeNode]) -> None:
            if node is None:
                return None
            
            yield from fetch_inorder(node.left)
            yield node
            yield from fetch_inorder(node.right)
        
        min_value = float("-inf")
        for node in fetch_inorder(root):
            if not min_value < node.val:
                return False
            min_value = node.val
        return True
```


## 2-4
- postorder recursive

```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        is_valid = True

        def traverse_and_fetch_min_max(root: Optional[TreeNode]) -> tuple[int, int]:
            nonlocal is_valid
            if root is None:
                return float("inf"), float("-inf")
            
            left_min, left_max = traverse_and_fetch_min_max(root.left)
            right_min, right_max = traverse_and_fetch_min_max(root.right)
            
            if not (left_max < root.val < right_min):
                is_valid = False
            return min(root.val, left_min), max(root.val, right_max)
    
        traverse_and_fetch_min_max(root)
        return is_valid
```


## 2-5
- postorder iterative

```py
from dataclasses import dataclass
from enum import Enum, auto


class Direction(Enum):
    up = auto()
    down = auto()


@dataclass
class MinMax:
    max_value: int | float = float("-inf")
    min_value: int | float = float("inf")



class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        stack = [(Direction.down, (root, MinMax(), None, None))]
        while stack:
            direction, args = stack.pop()
            if direction == Direction.down:
                node, ret, _, _ = args
                if node is None:
                    continue
                
                left_min_max = MinMax()
                right_min_max = MinMax()
                stack.append((Direction.up, (node, ret, left_min_max, right_min_max)))
                stack.append((Direction.down, (node.left, left_min_max, None, None)))
                stack.append((Direction.down, (node.right, right_min_max, None, None)))
            else:
                node, ret, left_ret, right_ret = args
                if not (left_ret.max_value < node.val < right_ret.min_value):
                    return False
                ret.max_value = max([node.val, left_ret.max_value, right_ret.max_value])
                ret.min_value = min([node.val, left_ret.min_value, right_ret.min_value])
        
        return True
```
