# 何も見ずに解く

## 考え
- 最初は問題文を理解できておらず 左の値 < node < 右の値 を繰り返して間違う

- 左の部分木の最大値 < node < 右の部分木の最小値を繰り返していけば解けそう
    - Time: O(N^2) Space: O(h) か
        - Pythonが 10^7 step / sec 処理できるとして、今回は 10sec くらいか
    - 無駄に２回走査しているが、他に考えが浮かばず
  
## 1-1
Time: O(N^2) 
Space: O(h)

```py
class Solution:
    @staticmethod
    def fetch_min_max(node: Optional[TreeNode]) -> tuple[int, int]:
        to_visit = [node]
        max_value = float("-inf")
        min_value = float("inf")
        while to_visit:
            node = to_visit.pop()
            if node is None:
                continue
            
            max_value = max(node.val, max_value)
            min_value = min(node.val, min_value)

            to_visit.append(node.left)
            to_visit.append(node.right)

        return min_value, max_value


    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        if root is None:
            raise ValueError("root is None")

        to_visit = [root]
        while to_visit:
            node = to_visit.pop()
            if node is None:
                continue

            _, left_max  = self.fetch_min_max(node.left)
            right_min, _ = self.fetch_min_max(node.right)
            if not (left_max < node.val < right_min):
                return False

            to_visit.append(node.left)
            to_visit.append(node.right)
        return True
```
