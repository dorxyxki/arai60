# 何も見ずに解く

## 考え
- 102 と同様に追加していくのに加えて、最後に値だけを反転させるのが簡潔か
- 探索自体をジグザグにすることでも解けそう

## 1-1
Time:  O(N)
Space: O(N)

```py
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
            
        values_by_level = []
        frontier = [root]
        is_reversed = False

        while frontier:
            next_frontier = []
            values = []
            
            for node in frontier:
                values.append(node.val)
                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)
            
            if is_reversed:
                values.reverse()
            values_by_level.append(values)
            frontier = next_frontier
            is_reversed = not is_reversed
        
        return values_by_level
```



## 1-2
- stack を使ってジグザグに探索していく
    - あまり直感的にかけなかった

Time:  O(N)
Space: O(N)

```py
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        values_by_level = []
        frontier = [root]
        is_reversed = False

        while frontier:
            next_frontier = []
            values = []
            
            while frontier:
                node = frontier.pop()
                if node is None:
                    continue
            
                values.append(node.val)
                if is_reversed:
                    next_frontier.append(node.right)
                    next_frontier.append(node.left)
                else:
                    next_frontier.append(node.left)
                    next_frontier.append(node.right)
            
            if values:
                values_by_level.append(values)
            frontier = next_frontier
            is_reversed = not is_reversed
        
        return values_by_level
```
