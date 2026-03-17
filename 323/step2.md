# 他の方の解法やディスコのログを見て解く

## 別の解法
- 隣接リスト or 隣接行列による DFS での探索
    - https://github.com/kitano-kazuki/leetcode/pull/20/changes#diff-1c1ba432dcdc5303b82497a1e18e8b8de244b90ea89f1ae7f001cb042d7153d8R5
    - https://github.com/mamo3gr/arai60/pull/56/changes#diff-9d599128cdcd703d6c0416a9b316e0411fc218403a5e3cf4d809eb9e52449966R5

## 参考
- 隣接リストを dict で表現するのか，list で表現するのか
    - https://github.com/sakupan102/arai60-practice/pull/22/changes#r1590628823
        > ノード数が決まっており、 0 から順番にノード番号が割り当てられているのであれば、node_to_adjacent_nodes = [[] for _ in range(n)]
        > としてしまう方法もあります。これにより定数倍高速化できる場合があります。理由は、 defaultdict の実装が CPython だとハッシュテーブルなのに対し、 list は可変長変数のためです。これにより、要素に参照する際のランダムメモリアクセスの回数が減ります。
        > ただし、定数倍の高速化が必要な場合に Python を使用するのは本末転倒な気もします。そういった場合は、 C++ 等、より高速な言語で書くほうが良いと思います。

    - 自分はメモリ消費の観点しか見れていなかった，O(1) の中でもこう言った観点がある. 
    - 注釈どおり，Pythonを使っている場合だとそもそも定数倍を気にするなら C++ 等で書こうとなるか
    
    - https://github.com/yas-2023/leetcode_arai60/pull/19/changes#r2441496173
        > 使い勝手はほぼ変わらず辞書の構築コストやハッシュの計算コストなどがかからないため若干効率的かもしれません。        
        - 辞書の構築コストも考慮にはいる．

    
- union find のクラス設計
    - https://github.com/mamo3gr/arai60/pull/56/changes#diff-fd96e9065c446f3d9690e8f736995a10d1e563aff6635b77e1b5c8c5e3c23adeR1
        - count メソッドを用意して，メンバ変数への直接的なアクセスを防いでいる？

- union find まとめ
    - https://github.com/naoto-iwase/leetcode/pull/28/changes#diff-4f6b01b75cf61fa706e6463e0a6840a6a0685f9f0cfcc46cc7dfb3530e908b18R122
    - 経路圧縮，木の高さを抑制する手法が綺麗にまとまっていた．
    - 経路圧縮
        - path halving と path splitting がある．
            - 実際に手元で圧縮してみてみると，path の変形がよくわかった．
            - このサイトが直感的に理解しやすかった https://37zigen.com/union-find/

    - 自分が union by rank と union by size をごっちゃに理解していた


## 2-1 
- 隣接リストと stack による探索，とりだしてから訪問済みを確認するので少し非効率
    - `nodes_to_check.extend(adjacent_nodes[node])` は node に対してたかだか1回呼ばれる．extend の total コストが2Eなので

Time:  O(N + E)
Space: O(N + E)

```py
class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        adjacent_nodes = [[] for _ in range(n)]
        for node1, node2 in edges:
            assert 0 <= node1 < n and 0 <= node2 < n
            adjacent_nodes[node1].append(node2)
            adjacent_nodes[node2].append(node1)

        visited = [False] * n

        def visit_connected_nodes(first_node: int) -> int:
            nodes_to_check = [first_node]
            while nodes_to_check:
                node = nodes_to_check.pop()
                if visited[node]:
                    continue

                visited[node] = True
                nodes_to_check.extend(adjacent_nodes[node])

        count = 0
        for node_index in range(n):
            if not visited[node_index]:
                visit_connected_nodes(node_index)
                count += 1

        return count
```

## 2-2

- 隣接行列と再帰での探索，次の探索に行く直前で確認する
    -  traverse(i) が呼ばれる回数はたかだか1回なので，traverse は N 回呼ばれる．

Time:  O(N^2)
Space: O(N^2)

```py
class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        is_connected = [[False] * n for _ in range(n)]

        for node1, node2 in edges:
            assert 0 <= node1 < n and 0 <= node2 < n
            is_connected[node1][node2] = True
            is_connected[node2][node1] = True

        visited = [False] * n

        def traverse(i: int) -> None:
            visited[i] = True
            for j in range(n):
                if is_connected[i][j] and not visited[j]:
                    traverse(j)

        num_connected = 0
        for i in range(n):
            if not visited[i]:
                traverse(i)
                num_connected += 1

        return num_connected
```
