# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

## 3-1

Time:  O(MN)
Space: O(MN)

```py
class Solution:
    WATER = "0"
    LAND = "1"
    DIRECTIONS = ((-1, 0), (1, 0), (0, -1), (0, 1))

    def numIslands(self, grid: List[List[str]]) -> int:
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
        
        def explore_island(row: int, col: int) -> None:
            frontier = [(row, col)]
            visited[row][col] = True

            while frontier:
                row, col = frontier.pop()
                for rd, rc in self.DIRECTIONS:
                    next_row = row + rd
                    next_col = col + rc
                    if inside_grid(next_row, next_col) and is_unvisited_land(next_row, next_col):
                        frontier.append((next_row, next_col))
                        visited[next_row][next_col] = True

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False for _ in range(num_cols)] for _ in range(num_rows)]
        count = 0       

        for row in range(num_rows):
            for col in range(num_cols):
                if inside_grid(row, col) and is_unvisited_land(row, col):
                    explore_island(row, col)
                    count += 1
        return count
```
