# 何も見ずに解く


## 考え
- DFS or BFS or Union-find で島を探索して，その際に area を増やすだけでいいのか？
- 再帰でもかけるか，受け取るのは grid の id と 返す area 
    - 再帰とDFSでかいてみる

## 1-1

Time:  O(MN)
Space: O(MN)

```py
class Solution:
    WATER = 0
    LAND = 1
    DIRECTIONS = ((-1, 0), (1, 0) ,(0, -1), (0, 1))

    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        if not grid:
            return 0

        def inside_grid(row: int, col: int) -> bool:
            if not 0 <= row < num_rows or not 0 <= col < num_cols:
                return False
            return True

        def is_unvisited_land(row: int, col: int) -> bool:
            if grid[row][col] == self.LAND and not visited[row][col]:
                return True
            return False

        def explore_and_measure(row: int , col: int) -> int:
            area = 1
            frontier = [(row, col)]
            visited[row][col] = True
            while frontier:
                row, col = frontier.pop()
                for rd, cd in self.DIRECTIONS:
                    new_row = row + rd
                    new_col = col + cd
                    if inside_grid(new_row, new_col) and is_unvisited_land(new_row, new_col):
                        frontier.append((new_row, new_col))
                        visited[new_row][new_col] = True
                        area += 1
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



## 1-2
- 未探索の陸地のみ訪問し area を足していく，行きがけに全ての処理を終えて，帰りがけは area の伝達のみ．

Time:  O(MN)
Space: O(MN)

```py
class Solution:
    WATER = 0
    LAND = 1
    DIRECTIONS = ((-1, 0), (1, 0) ,(0, -1), (0, 1))

    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        if not grid:
            return 0

        def inside_grid(row: int, col: int) -> bool:
            if 0 <= row < num_rows and 0 <= col < num_cols:
                return True
            return False

        def is_unvisited_land(row: int, col: int) -> bool:
            if grid[row][col] == self.LAND and not visited[row][col]:
                return True
            return False

        def explore_island(row: int , col: int, area: int) -> int:
            visited[row][col] = True
            for rd, cd in self.DIRECTIONS:
                new_row = row + rd
                new_col = col + cd
                if inside_grid(new_row, new_col) and is_unvisited_land(new_row, new_col):
                    area = explore_island(new_row, new_col, area + 1)

            return area
            

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False for _ in range(num_cols)] for _ in range(num_rows)]

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                if is_unvisited_land(row, col):
                    area = explore_island(row, col, 1)
                    max_area = max(area, max_area)
        return max_area
```
