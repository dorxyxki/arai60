# 何も見ずに解く

## 考え
- まず最初に与えられた配列から作成されるBSTの root の位置になるノードは真ん中の要素を使えばいい
- 左右にそれ以外の配列の要素からなるノードを吊るすことを考える
- そうすると、バランスが取れていないBSTなら作れそう

- 今度は真ん中の要素で分かれた右と左の配列について、部下に同じことをやってもらって、完成したものを root の右左につければ良さそう
- そうすると再帰だと自然にコードに落とし込める

N <= 10^4

## 1-1
- 考えをそのままコードに落とし込んだもの。再帰で解くと、python のデフォルトの再帰上限に引っかかりそう。

Time:  O(N) 
Space: O(N)

```py
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None

        root_pos = (len(nums) - 1) // 2
        new_node = TreeNode(val=nums[root_pos])
        new_node.left = self.sortedArrayToBST(nums[:root_pos])
        new_node.right = self.sortedArrayToBST(nums[root_pos+1:])
        return new_node
```


## 1-2
- スタックによるDFSかつ、親に繋いでいく

Time:  O(N)
Space: O(N)

```py
from enum import auto


class Direction(enum.Enum):
    left = auto()
    right = auto()


class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        dummy = TreeNode()
        to_attach = [(dummy, Direction.left, nums)]
        while to_attach:
            parent, direction, source = to_attach.pop()
            if not source:
                continue
                
            root_pos = (len(source) - 1) // 2
            new_node = TreeNode(source[root_pos])
            setattr(parent, direction.name, new_node)
            to_attach.append((new_node, Direction.left, source[:root_pos]))
            to_attach.append((new_node, Direction.right, source[root_pos+1:]))
        return dummy.left
```
