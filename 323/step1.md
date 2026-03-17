# 何も見ずに解く

## 考え
- Unionfindそのままの操作だったので，Unionfindで繋がっているグラフの数を管理する．
    - Time O(M * log(N)) ? log(N) は最悪ケースなので，償却するとアッカーマン関数の逆関数でほぼ定数になる？
    - Space O(N)

- UnionFindは常識に含まれない気がしたので，他の解き方を考えてみるがそれ以外の解法が思いつかず
    - エッジから隣接リスト，隣接行列に変換する考えがでてこず

Time:  O(N + Elog(N)) ?
Space: O(N)

```py
class UnionFind:
    def __init__(self, num_nodes: int) -> None:
        self.parent = [i for i in range(num_nodes)]
        self.rank = [1] * num_nodes
        self.num_groups = num_nodes
    
    def find(self, node: int) -> int:
        if self.parent[node] != node:
            self.parent[node] = self.find(self.parent[node])
        return self.parent[node]

    def union(self, node1: int, node2: int) -> None:
        smaller = self.find(node1)
        bigger = self.find(node2)
        if smaller == bigger:
            return
        
        if self.rank[smaller] > self.rank[bigger]:
            smaller, bigger = bigger, smaller
        self.parent[smaller] = bigger
        if self.rank[smaller] == self.rank[bigger]:
            self.rank[bigger] += 1
        self.num_groups -= 1


class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        uf = UnionFind(n)
        for node1, node2 in edges:
            assert node1 != node2
            assert 0 <= node1 < n and 0 <= node2 < n

            uf.union(node1, node2)
        return uf.num_groups
```
