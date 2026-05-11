# 何も見ずに解く

## 考え
- BFS で探索すると、レベル順に走査できる。
    - 実装も deque を使ったものではなく、level ごとでの取り回しを行う方が今回は理解しやすそう
- 結果に値を追加していくのは frontier から取り出した時が直感的だと感じた

## 1-1

Time:  O(N)
Space: O(N)

```py
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        vals_by_level = []
        frontier = [root]

        while frontier:
            next_frontier = []
            node_vals = []

            for node in frontier:
                if node is None:
                    continue

                node_vals.append(node.val)
                next_frontier.append(node.left)
                next_frontier.append(node.right)
            
            if node_vals:
                vals_by_level.append(node_vals)
            frontier = next_frontier
        
        return vals_by_level
```
