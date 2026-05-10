# 何も見ずに解く

## 考え
- 根から葉っぱへの path を探索する時には dfs で探索したほうが bfs より早く回答が見つかる可能性があるので dfs が良さそう

## 1-1
Time:  O(N)
Space: O(N)

```py
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        def is_leaf(node: TreeNode) -> bool:
            return node.left is None and node.right is None
        
        path_candidates = [(root, 0)]
        while path_candidates:
            node, path_sum = path_candidates.pop()
            if node is None:
                continue

            path_sum += node.val
            if is_leaf(node) and path_sum == targetSum:
                return True

            path_candidates.append((node.left, path_sum))
            path_candidates.append((node.right, path_sum))

        return False
```

## 1-2
- 練習として再帰で同じことを書いてみる
    - defaultの再帰上限とデバックのしづらさには気をつける

Time:  O(N)
Space: O(N)

```py
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        def traverse_path(node: Optional[TreeNode], path_sum: int) -> bool:
            if node is None:
                return False

            path_sum += node.val
            if node.left is None and node.right is None and path_sum == targetSum:
                return True

            return traverse_path(node.left, path_sum) or traverse_path(node.right, path_sum)

        return traverse_path(root, 0)            
```
