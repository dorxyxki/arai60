# 何も見ずに解く

## 考え
- 島の判定自体は深さ優先探索と幅優先探索でいけそうだな
  - でも，次の島にどうやって行こう
  - 愚直に探すしかないか
- grid が空の場合だとおちるけど，島ないから0で返すのがいいかな
- grid を書き換えるのは使う人が想定していないと仮定してやる（島の個数を聞いて島がなくなったら困りそう）

- DFSは再帰でも stack でもかけるはず，python のデフォルトの recursion limit が 1,000 なので再帰で書くなら念頭に置いておくひつようありそう
    - BFSは探索の仕方が変わるだけでコードはほぼ変わらないか


## 1-1

Time:  O(NM) ?
Space: O(NM)

```py
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0

        def try_add_island(i: int, j: int) -> None:
            if i >= M or j >= N or i < 0 or j < 0:
                return
            
            if grid[i][j] == "1" and frontier[i][j]:
                frontier[i][j] = False
                islands.append((i, j))

        M = len(grid)
        N = len(grid[0])

        frontier = [[True for _ in range(N)] for _ in range(M)]        
        count = 0
        
        for m in range(M):
            for n in range(N):
                if not frontier[m][n] or grid[m][n] == "0":
                    continue

                count += 1

                islands = []
                i, j = m, n
                frontier[i][j] = False
                islands.append((i, j))
                while islands:
                    i, j = islands.pop()
                    try_add_island(i - 1, j)
                    try_add_island(i + 1, j)
                    try_add_island(i, j - 1)
                    try_add_island(i, j + 1)
                    
        return count
```
