# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1
- この問題の要求だと BFS は DFS のように前探索する必要がないので BFS をえらぶ
- 今回はdepth と frontier を明示的に分けて書く．こちらの方が 1 層ずつ下がりながら探索する感じが掴みやすい．

```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if root is None:
            return 0

        frontier = [root]
        depth = 1
        while frontier:
            next_frontier = []
            for node in frontier:
                if node.left is None and node.right is None:
                    return depth
                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)

            frontier = next_frontier
            depth += 1

        assert False, "unreachable"
```
