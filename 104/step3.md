# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

- python の再帰上限を考えて，stack + loop で書く
- 空間計算量の視点から，木の形が事前にわかっていて，平衡に近いなら DFS, 一直線みたいな形なら BFS がいいのかな
- とりあえず node is None でも スタックに入れてしまう方式．条件分岐を減らせる方を優先した．空間計算量は少し犠牲になるか．

```py
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        max_depth = 0
        frontier = [(root, 1)]
        while frontier:
            node, depth = frontier.pop()
            if node is None:
                continue
            max_depth = max(depth, max_depth)
            frontier.append((node.left, depth + 1))
            frontier.append((node.right, depth + 1))
        return max_depth
```
