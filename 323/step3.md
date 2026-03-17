# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

```py
from collections import defaultdict


class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        node_to_adjacent_nodes = defaultdict(list)

        for node1, node2 in edges:
            node_to_adjacent_nodes[node1].append(node2)
            node_to_adjacent_nodes[node2].append(node1)

        scheduled = [False] * n
        def visit_connected_node(node: int) -> None:
            node_to_visit = [node]
            scheduled[node] = True

            while node_to_visit:
                node = node_to_visit.pop()
                for adjacent_node in node_to_adjacent_nodes[node]:
                    if not scheduled[adjacent_node]:
                        node_to_visit.append(adjacent_node)
                        scheduled[adjacent_node] = True

        count = 0
        for node in range(n):
            if not scheduled[node]:
                count += 1
                visit_connected_node(node)

        return count
```
