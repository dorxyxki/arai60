# 他の方の解法やディスコのログを見て解く

## 参考
- isort
    - 二部探索後に挿入までおこなう．挿入点から後ろまで拡張する？ので，O(N)かかる
    - https://docs.python.org/ja/3/library/bisect.html#bisect.insort
        > Keep in mind that the O(log n) search is dominated by the slow O(n) insertion step.
    
- heapq
    - heapify: 時間計算量は線形に https://docs.python.org/ja/3/library/heapq.html#heapq.heapify
    - priority queue という抽象データ型の実装の一つ．他にも色々あるらしい
        - 
    - https://github.com/cheeseNA/leetcode/pull/12/files#r1543919411
    - heappushpop heapreplace とうもある
        https://docs.python.org/ja/3/library/heapq.html#heapq.heappushpop
        > この一続きの動作を heappush() に引き続いて heappop() を別々に呼び出すよりも効率的に実行します。
    
- quick select
    - https://discord.com/channels/1084280443945353267/1183683738635346001/1185972070165782688
        > Top K を見つけるには、priority_queue に放り込むのでもいいですが、計算量としてソートしているのと変わらないですね。

        > QuickSelect というアルゴリズムがあります。だいたい、クイックソートを途中までやるというアルゴリズムです。
    - quick sort を触る時に後ほどこちらもさわる


## 至れなかった考え
- 想定外の入力について
    - k がマイナス等の場合の考慮
        - https://github.com/katataku/leetcode/pull/8/changes#r1856437996
            > 負の場合、[:k] のところで、予期しないスライスができるか落ちるかで、中途半端にそれらしい値が出てくることになるでしょう。
            > あまり意味のないものがそれっぽく動き続けるのは、わりとデバッグのときに困ります。
        - 今回はテストの足切りを決める場合などのケースを考えると，それっぽい値を出されたらとても困るので，エラー出して止めよう

- 呼び出す人のことを考える
    - 呼び出し側の nums を破壊的に変更することについての考慮が足りなかった．
    - https://github.com/rinost081/LeetCode/pull/9/changes#r1874734978
        > 関数を呼び出す側としては、ある関数を呼び出したときに、渡した引数の中身が変更されることは、あまり想定しないように思います。

- k 番目まで大きい値を入れるデータの処理を while で行う視野も
    - https://github.com/liruly/leetcode/pull/10/changes#r2702438585
        > 確かにカプセル化に失敗したりで外部からデータを操作され想定以上のデータが入っていた場合の防御策にはなりますね。
    - https://github.com/mamo3gr/arai60/pull/25#discussion_r2700753824
        > if だと「インデックスが範囲外になる場合のずれは高々1だけのはずだ」という、外部のロジックへの依存・信頼が必要になり、読むときも俯瞰的に読まないといけません。
        > while であれば、「配列長が足りなければ足りるまで伸ばす」というシンプルで独立したロジックとして完結しているため、局所的にパッと見たときの安心感があります。
    - どちらのご意見とも賛同させていただきます．

- python のアンスコをつける命名について
    - https://docs.python.org/ja/3/tutorial/classes.html#private-variables
        > オブジェクトの中からしかアクセス出来ない "プライベート" インスタンス変数は、 Python にはありません。しかし、ほとんどの Python コードが従っている慣習があります。アンダースコアで始まる名前 (例えば _spam) は、 (関数であれメソッドであれデータメンバであれ) 非 public なAPIとして扱います。これらは、予告なく変更されるかもしれない実装の詳細として扱われるべきです。
    - https://peps.python.org/pep-0008/#descriptive-naming-styles
        > _single_leading_underscore: weak “internal use” indicator. E.g. from M import * does not import objects whose names start with an underscore.
    

## 2-1
- heapq を利用する場合のリファクタ
- 与えられた配列を heap にして，それから N-k 要素を削除．
- init 時間計算量 O(N + (N-k)logN) 空間計算量 O(N)
- add  時間計算量 O(logk)  空間計算量 O(1)

```py
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        if not 1 <= k <= len(nums) + 1:
            raise ValueError("k is out of range. k must be between 1 and len(nums) + 1")
        
        self._k = k
        self._topk_largest_scores = nums.copy()
        heapq.heapify(self._topk_largest_scores)

        while len(self._topk_largest_scores) > k:
            heapq.heappop(self._topk_largest_scores)

    def add(self, val: int) -> int:
        heapq.heappush(self._topk_largest, val)
        while len(self._topk_largest) > self._k:
            heapq.heappop(self._topk_largest)
        return self._topk_largest[0]
```

## 2-2
- 常に，len(_topk_largest) <= (k+1) を保証することで，N-k 回の削除を消した．
- 初期化のところ push で heap を作っていくことで，処理の共通化ができる．
    - https://github.com/ksaito0629/leetcode_arai60/pull/4/changes#diff-9d5937a1140f3f570c550733191137cfe4f5debb37c3ba41568adfc2eb85dff8R96
    - push, pop がまとまっていて読みやすい．
    - 保守性という観点も
        - https://github.com/Hiroto-Iizuka/coding_practice/pull/8/changes#r2659016808
            >  ここでは条件分岐が不要だから条件分岐を抜いた関数を用意すると速いぞ、みたいなことを考えるかもしれませんが、その場合は「速さ」が具体的に何秒なのかを見積もってください。そのメリットと上のような管理コストの増大を比較することになります。

- init 時間計算量 O(Nlogk) 空間計算量 O(k)
- add  時間計算量 O(logk)  空間計算量 O(1)

```py
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        if not 1 <= k <= len(nums) + 1:
            raise ValueError("k is out of range. k must be between 1 and len(nums) + 1")
        
        self._k = k
        self._topk_largest = []

        for num in nums:
            _ = self.add(num)

    def add(self, val: int) -> int:
        heapq.heappush(self._topk_largest, val)
        while len(self._topk_largest) > self._k:
            heapq.heappop(self._topk_largest)
        return self._topk_largest[0]
```

## 2-3
- ソート済みを使用して add の際に二部探索+挿入をする
- init 時間計算量 O(NlogN) 空間計算量 O(N)
- add  時間計算量 O(N)  空間計算量 O(1)
- 早い．
    - 知らなかった．
    - https://github.com/fuga-98/arai60/pull/9#discussion_r1966724064
        > アルゴリズムの中身はずらしているんで計算量的には遅いですが、Python ではネイティコードとして動くので速いです。
    - https://github.com/tNita/arai60/pull/9/changes
        > 定数倍が重要な例

```py
class KthLargest:

    def __init__(self, k: int, nums: List[int]):
        if not 1 <= k <= len(nums) + 1:
            raise ValueError("k is out of range. k must be between 1 and len(nums) + 1")

        self._k = k
        self._topk_largests = sorted(nums)[:-k]

    def add(self, val: int) -> int:
        bisect.insort(self._topk_largests, val)
        return self._topk_largests[-self._k]
```
