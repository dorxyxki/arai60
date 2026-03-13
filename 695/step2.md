# 他の方の解法やディスコのログを見て解く


## 別の解法
- union-find を使用して解くことも

- BFS，DFSのキュー，スタックにとりあえず隣接セルを入れてしまって，取り出してから条件分岐やカウントなどをする方針（不変条件が異なる）
    - こちらの方が，キュー，スタックに何が入っているのか，visited, area を記録するタイミングが直感的でよかった．
    - ただ，計算量が変わる（無駄な探索が増えるので）のに注意か

    - https://github.com/colorbox/leetcode/pull/32/changes#diff-bcd2b96dbe0ac748924c9625d1b1fc0764b0fc4fda9479beeb7c340d9cbb6122R38
    - https://github.com/naoto-iwase/leetcode/pull/18/changes#r2424179923
    - https://github.com/sakupan102/arai60-practice/pull/19/changes#r1585944741

- 再帰の別の方法
    - 1-2 では有効な未探索地のみを探索したが，とりあえず探索してみて，条件を満たさなかったら終端条件で帰る方法．
        - 上記のキュースタックにとりあえず入れて，取り出してから考える方針に似ている

    - https://github.com/ksaito0629/leetcode_arai60/pull/17/changes#diff-f900d935b098a7e0624f0f051d6c3743f747f53f1c8138648b6a28c088f2f383R183
    - https://github.com/kitano-kazuki/leetcode/pull/18/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R159

## 参考
- `return 条件` の書き方が簡潔でいい
    - https://github.com/n6o/leetcode_arai60/pull/18/changes#diff-83c7941d06b952f77f8ed25bdf52322f3588314fb73e5da782120289b2c4314fR194

- area を増やす時のタイミング
    - https://github.com/naoto-iwase/leetcode/pull/18/changes#r2424179923
        > popleft するときに増やすほうが素直ではないですか?
        > つまり、これは、追加時に数えてますが、出てくるときに数えるのもあるということです。
    - たしかに，出てくる時に判定する方が自然か

- union find の経路圧縮の書き方
    - https://github.com/Yuto729/LeetCode_arai60/pull/23/changes#diff-80b7b5fcf1151e830e7234c976ca634a6b67fca3eb54ab4a3edd2f34a31ef3a3R175
    - https://github.com/kitano-kazuki/leetcode/pull/18/changes#r2902998377
        > 自然言語にすると、自分の根が自分じゃないならば、自分の親を自分の根につけかえて、自分の親（根）を返す。という感じですね。 

- frontier について
    - https://github.com/mamo3gr/arai60/pull/18/changes#diff-38edbb45b15ec06dae8ba07784b57d282281f183a17e590d7127182d6c8b84feR18
        > ちょっと frontier という単語に多くの情報を伝えることを期待しすぎなきらいはある。
    - 確かに自分が書いたコードでは グリッド内かつ未探索な陸地という意味を包含していたりする．ちょっと長くすべきか


## 2-1
- 再帰書き直し

```py
class Solution:
    WATER = 0
    LAND = 1
    DIRECTIONS = ((-1, 0), (1, 0) ,(0, -1), (0, 1))

    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        if not grid:
            return 0

        def inside_grid(row: int, col: int) -> bool:
            return 0 <= row < num_rows and 0 <= col < num_cols

        def is_unvisited_land(row: int, col: int) -> bool:
            return not visited[row][col] and grid[row][col] == self.LAND

        def explore_and_measure(row: int, col: int) -> int:
            if not (inside_grid(row, col) and is_unvisited_land(row, col)):
                return 0

            visited[row][col] = True
            area = 1
            for rd, rc in self.DIRECTIONS:
                area += explore_and_measure(row + rd, col + rc)
            return area

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False for _ in range(num_cols)] for _ in range(num_rows)]

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                if is_unvisited_land(row, col):
                    area = explore_and_measure(row, col)
                    max_area = max(area, max_area)
        return max_area
```

## 2-2
- BFS
    - キューに訪問したい土地を入れる（とりあえず），取り出す時に条件が揃った土地なら，訪問済みにして島の面積を増やす
    - こちらの方が直感的かもしれない？

```py
from collections import deque


class Solution:
    WATER = 0
    LAND = 1
    DIRECTIONS = ((-1, 0), (1, 0) ,(0, -1), (0, 1))

    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        if not grid:
            return 0

        def inside_grid(row: int, col: int) -> bool:
            return 0 <= row < num_rows and 0 <= col < num_cols
        
        def is_unvisited_land(row: int, col: int) -> bool:
            return not visited[row][col] and grid[row][col] == self.LAND
        
        def explore_island(row: int , col: int) -> int:
            cell_to_visit = deque()
            cell_to_visit.append((row, col))
            area = 0

            while cell_to_visit:
                row, col = cell_to_visit.popleft()
                if not (inside_grid(row, col) and is_unvisited_land(row, col)):
                    continue
                
                area += 1
                visited[row][col] = True
                cell_to_visit.append((row - 1, col))
                cell_to_visit.append((row + 1, col))
                cell_to_visit.append((row, col - 1))
                cell_to_visit.append((row, col + 1))

            return area

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]
        
        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                area = explore_island(row, col)
                max_area = max(area, max_area)

        return max_area
```

## 2-3
- 1-1 のリファクタ

```py
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        if not grid:
            return 0

        WATER = 0
        LAND = 1

        def try_expand_frontier(row: int , col: int, frontier: list) -> None:
            if not (0 <= row < num_rows and 0 <= col < num_cols):
                return
            if visited[row][col] or grid[row][col] == WATER:
                return
            
            frontier.append((row, col))
            visited[row][col] = True
            
        def explore_and_measure(row: int , col: int) -> int:
            frontier = []
            try_expand_frontier(row, col, frontier)
            area = 0
            while frontier:
                row, col = frontier.pop()
                area += 1

                try_expand_frontier(row - 1, col, frontier)
                try_expand_frontier(row + 1, col, frontier)
                try_expand_frontier(row, col - 1, frontier)
                try_expand_frontier(row, col + 1, frontier)

            return area

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                area = explore_and_measure(row, col)
                max_area = max(area, max_area)
        return max_area
```