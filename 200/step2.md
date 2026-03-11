# 他の方の解法やディスコのログを見て解く


## 別の解法
- Union find で島の判定をおこなうことも
    - https://github.com/kitano-kazuki/leetcode/pull/17/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R245

- 未開拓地を管理するのではなkう，訪問済みを管理する(visited 等)
    - https://github.com/ksaito0629/leetcode_arai60/pull/16/changes#diff-aebad28eff850d7a6e1ef6430490e8350ba9ea923904cb57cfc959d63fbed767R183

## 参考
- 隣接ノードを見る方向を定義しておく
    - https://github.com/kitano-kazuki/leetcode/pull/17/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R20
        > dirs = [(1, 0), (0, -1), (-1, 0), (0, 1)]
    - わかりやすい

- 関数化する部分について
    - 一つの島を探索するという意味単位で関数に落とし込んでおり，わかりやすい
        - https://github.com/kitano-kazuki/leetcode/pull/17/changes
        - https://github.com/n6o/leetcode_arai60/pull/17/changes
        - https://github.com/ksaito0629/leetcode_arai60/pull/16/changes

- 変数名について
    - i, j ではなく row, col という選択肢
        - https://github.com/mamo3gr/arai60/pull/16#discussion_r2656178531
            > 個人的には二次元座標に (i, j) を使うのは違和感を感じます。 

    - explore 
        - https://github.com/n6o/leetcode_arai60/pull/17/changes#diff-15487a7c78765d56a7dcaab622b527797af995658959b44e6739a9a1ade8edb9R130
        - 個人的にはこの関数名がしっくりくる

    - WATER, ISLAND
        - https://github.com/n6o/leetcode_arai60/pull/17/changes#diff-15487a7c78765d56a7dcaab622b527797af995658959b44e6739a9a1ade8edb9R153
        - https://github.com/Ryotaro25/leetcode_first60/pull/18/changes/5ef4f552cb2fd95e75b0f67832372b71a80b0c20#r1676765150
            > '1'とか'0'がなんだかわからなくなるので、別途LAND = '1' WATER='0'と定めておいて grid[i][j] == LAND などとする選択肢がありますね
    
    - 定数について
        - https://github.com/mamo3gr/arai60/pull/16/changes#r2656176199
            > あくまで個人の感覚になりますが、プロセスが起動してから終了するまで変更されない値を定数と呼ぶように思います。入力によって値が書き換わる場合は、定数とはみなさないと思います。おそらく常識の範囲だと思います。
        - なるほど

- メンバ変数とスレッド並列正について
    - https://github.com/colorbox/leetcode/pull/31#discussion_r1881098955
        > これをメンバ変数で持つと、numIslands のスレッド並列性が失われます。失っては絶対にいけないわけではないですが、機能の複雑性とのバランスを考えたときに失うほどのものかとは思います。
    - https://github.com/Ryotaro25/leetcode_first60/pull/20#discussion_r1685631456
        > visited をクラスのメンバー変数として持つと、複数のスレッドから同じインスタンスの countComponents() を同時に呼び出したときに、スレッド競合が起こります。 
    
- 条件を複数行書きたい時
    - https://google.github.io/styleguide/pyguide.html#32-line-length
        > Do not use a backslash for explicit line continuation.
        > Instead, make use of Python’s implicit line joining inside parentheses, brackets and braces. If necessary, you can add an extra pair of parentheses around an expression.

- スコープと束縛について    
    - https://github.com/naoto-iwase/leetcode/pull/17/changes#diff-5c15b5a457745340b0829a41cc85d0ec21654a482447ccb1facec02bdcd5e432R245
        > # コンパイラはまずinner()の中にあるx = [99, 2, 3]をみて、変数xはinnerのローカル変数と決めてしまう
        > # その上でx[0] = 99を実行しようとして、まだ値が束縛されていないローカル変数にアクセスしようとするから、UnboundLocalError
    - しらなかった．

- Union find にて 木の高さを管理する方法
    - path compressoin: find したノードの親を根にする
        - https://github.com/ksaito0629/leetcode_arai60/pull/16/changes#diff-aebad28eff850d7a6e1ef6430490e8350ba9ea923904cb57cfc959d63fbed767R107
        - 再帰で書いた方が直感的だな，union by rank しておけばスタックの上限も O(logN) になる．

    - union by rank: 木の高さをたかだか logN にできる
        - https://github.com/kitano-kazuki/leetcode/pull/17/changes#diff-0c860cd754249868513e4f9054206317fa33d0f548fc3896ac2b3e11822fd852R270
        - https://github.com/ksaito0629/leetcode_arai60/pull/16/changes#diff-aebad28eff850d7a6e1ef6430490e8350ba9ea923904cb57cfc959d63fbed767R139
        - https://discord.com/channels/1084280443945353267/1201211204547383386/1213387878734766080
            > 私の感覚なんですけど、日本語だったらこう表現すると思うんですよ。「root1 と root2 の集団のサイズを比べて、小さい方を大きい方に合流させる。」

- Union find の設計
    - https://github.com/ichika0615/arai60/pull/9/changes#r1954436002
        > UnionFind の仕事をしてくれる人間いるとしましょう。自分は、numIslands の仕事をしていて電話をかけます。UnionFind に3種類の電話をかけているわけですが、つまり、それぞれ抽象的には「初期化」「2つが繋がっているので繋いでくれ」「ある陸地の親はどこか」を連絡しているわけですね。


## 2-1
- DFSを再帰で行う

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

        def is_valid_frontier(row: int, col: int) -> bool:
            if not (0 <= row < num_rows) or not (0 <= col < num_cols):
                return False
            if grid[row][col] == self.WATER or visited[row][col]: 
                return False
            return True

        def explore_island(row: int, col: int) -> None:
            visited[row][col] = True
            for row_dir, col_dir in self.DIRECTIONS:
                next_row = row + row_dir
                next_col = col + col_dir
                if is_valid_frontier(next_row, next_col):
                    explore_island(next_row, next_col)

        
        num_rows = len(grid)
        num_cols = len(grid[0])
        
        visited = [[False for _ in range(num_cols)] for _ in range(num_rows)]
        count = 0

        for r in range(num_rows):
            for c in range(num_cols):
                if is_valid_frontier(r, c):
                    explore_island(r, c)
                    count += 1

        return count
```

## 2-2
- union-find を利用する（Disjoint setの数を都度更新する）

Time:  O(MN)
Space: O(MN)

```py
class Solution:
    WATER = "0"
    LAND = "1"
    DIRECTIONS = ((1, 0), (0, 1))

    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0

        def get_flattened_index(row: int, col: int) -> int:
            return row * num_cols + col

        def try_joint_island(row: int, col: int, direction: tuple[int, int]) -> None:
            next_row = row + direction[0]
            next_col = col + direction[1]

            if next_row >= num_rows or next_col >= num_cols:
                return
            if grid[next_row][next_col] == self.WATER:
                return
            
            uf.union(get_flattened_index(row, col), get_flattened_index(next_row, next_col))

        num_rows = len(grid)
        num_cols = len(grid[0])
        flatten = []
        for row in range(num_rows):
            flatten.extend(grid[row])
        
        uf = UnionFind(flatten)
        for row in range(num_rows):
            for col in range(num_cols):
                if grid[row][col] == self.WATER:
                    continue

                for direction in self.DIRECTIONS:
                    try_joint_island(row, col, direction)
        
        return uf.num_disjoint_set

        
class UnionFind:
    def __init__(self, arr: list) -> None:
        self.parent = []
        self.num_disjoint_set = 0
        self.rank = [1] * len(arr)
        for i, flag in enumerate(arr):
            self.parent.append(i)
            if flag == "1":
                self.num_disjoint_set += 1

    def find(self, i: int) -> int:        
        node = i
        while node != self.parent[node]:
            self.parent[node] = self.parent[self.parent[node]]
            node = self.parent[node]
        self.parent[i] = node
        return node
    
    def union(self, i: int, j: int) -> None:
        smaller = self.find(i)
        larger = self.find(j)
        if smaller == larger:
            return

        if self.rank[smaller] > self.rank[larger]:
            smaller, larger = larger, smaller
        self.parent[smaller] = larger

        if self.rank[smaller] == self.rank[larger]:
            self.rank[larger] += 1
        self.num_disjoint_set -= 1
```

## 2-3
- 2-2 のリファクタ
    - union-find には島の作成と島をつなげる仕事と島の数の管理をお願いする．"1" が陸地であるような情報を知っているのは numIsland にいる人だけでいいか
    - parent の表現を配列ではなく 辞書にすれば grid のインデックスを 1 次元上のインデックスに修正する必要がない．   

```py
class Solution:
    WATER = "0"
    LAND = "1"
    DIRECTIONS = ((1, 0), (0, 1))

    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0

        def is_valid_frontier(row: int, col: int) -> bool:
            if row >= num_rows or col >= num_cols:
                return False
            if grid[row][col] == self.WATER:
                return False
            
            return True

        def joint_neighbor(row: int, col: int) -> None:
            for row_dir, col_dir in self.DIRECTIONS:
                next_row = row + row_dir
                next_col = col + col_dir
                if is_valid_frontier(next_row, next_col):                
                    island_manager.union((row, col), (next_row, next_col))

        num_rows = len(grid)
        num_cols = len(grid[0])
        island_manager = UnionFind()

        for row in range(num_rows):
            for col in range(num_cols):
                if is_valid_frontier(row, col):
                    island_manager.add((row, col))
                    joint_neighbor(row, col)
        
        return island_manager.num_disjoint_set

    
class UnionFind:
    def __init__(self) -> None:
        self.num_disjoint_set = 0
        self.parent = dict()
        self.rank = dict()
    
    def add(self, node: tuple) -> None:
        if node in self.parent:
            return
            
        self.parent[node] = node
        self.rank[node] = 1
        self.num_disjoint_set += 1

    def find_root(self, node: tuple) -> tuple:
        if self.parent[node] == node:
            return node
        
        root = self.find_root(self.parent[node])
        self.parent[node] = root
        return root

    def union(self, node1: tuple, node2: tuple) -> None:
        self.add(node1)
        self.add(node2)
        smaller = self.find_root(node1)
        larger = self.find_root(node2)
        if smaller == larger:
            return 
        
        if self.rank[smaller] > self.rank[larger]:
            smaller, larger = larger, smaller
        self.parent[smaller] = larger

        if self.rank[smaller] == self.rank[larger]:
            self.rank[larger] += 1
        self.num_disjoint_set -= 1
```
