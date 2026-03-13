# 10 分程度で一回もエラーを出さずに書ける状態になるまで3回続ける

```py
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        if not grid:
            return 0

        WATER = 0
        LAND = 1

        def inside_grid(row: int, col: int) -> bool:
            return 0 <= row < num_rows and 0 <= col < num_cols
        
        def is_frontier(row: int, col: int) -> bool:
            return not visited[row][col] and grid[row][col] == LAND

        def explore_and_measure(row: int, col: int) -> int:
            cell_to_check = [(row, col)]
            area = 0
            while cell_to_check:
                row, col = cell_to_check.pop()
                if not (inside_grid(row, col) and is_frontier(row, col)):
                    continue
                
                area += 1
                visited[row][col] = True
                cell_to_check.append((row - 1, col))
                cell_to_check.append((row + 1, col))
                cell_to_check.append((row, col - 1))
                cell_to_check.append((row, col + 1))

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
