# 他の方の解法やディスコのログを見て解く

## 他の解法
- priority queue で過去に見たことのあるペアと和を状態として保存する．
    - pop して出てきたペアを (i,j) として次の候補となるのは (i+1, j) (i, j+1) だけというもの．
    - 大まかに3つやりかたがあるらしい．
        1. 訪問済みの状態をもう一つ用意することで重複をなくす．
        2. はじめに(i,0) をすべていれて (i, k) がでてくると (i, k + 1) を入れるという方法
        3. さらに i,j の周りの状況から候補に入れるかを絞ることも可能.
        - https://github.com/Yoshiki-Iwasa/Arai60/pull/9#discussion_r1647019606

    - 2次元のグリッドを書いてみると，どんな候補が有効なのか直感的に理解できた．
        - 本当に有効な候補となるのは角っこだけなのか． 
            - pop で (4, 8) がでても， 
                - (6, 8) は (6, 7) が確定してないから候補に入れなくてもいい
                - (4, 12) もおなじ．かならず (2, 12) より大きくなるから候補に入れる必要なし．
            
            ```
            i/j     2      4      6      9
            1       ■3     ■5     ■7     ■10
            7       ■9     ■11    ⭕13   ·16
            8       ■10    ■12    ·14    ·17
            12      ⭕14   ·16    ·18    ·21
            ```

        - https://discord.com/channels/1084280443945353267/1183683738635346001/1187326805015810089
            > 私だったら紙と鉛筆だけでやれといわれたら、10問目は、エクセルみたいな表を作ります。
            > 左にnums1、上にnums2を書きます。で、左上が、絶対に一番小さいじゃないですか。
            > まさか、全部の表を手作業で埋めて、全部比較してから一番小さいやつは、これ、ってやらないと思うんですよ。
            > そうして、小さい順に s 個出力したとして、その次に小さいやつは、もうすでに使ったやつの右か下にあるやつの中にあるはずです。それを素直にコーディングするとどうなりますか。ということです。
            - グリッドをかけるか，状態管理をしたくなるか，それに適したデータ型を思いつくかどうか

- generator を使った解法
    - https://discord.com/channels/1084280443945353267/1235829049511903273/1246118347863621652
    - https://github.com/nittoco/leetcode/pull/33/changes#diff-ad8e6f74b1931125ea16495f7243b4554871dfc6cb857f22b2e2174b5bc4737aR119
    - https://github.com/naoto-iwase/leetcode/pull/10/changes#diff-286fb5038eb6a401d524f96be2232f62da7edf98e92fb04003be9fcc29b20eb8R156
    - こんな解き方もあるのかーくらいにしか認識できなかった．


## 考えられなかったこと
- 実際にどれくらいのメモリ消費量かの見積もりをすること
    - https://github.com/mamo3gr/arai60/pull/10#discussion_r2646805422
        > 最大サイズ：Constraintsの 1 <= nums1.length, nums2.length <= 10^5 から 10^5
        > 1要素あたりのサイズ：28バイト
        > sys.getsizeof(10**9) より
        > 28 * 10^5 * 10^5 = 280 GB 
    
    - sys.getsizeof() をつかうといいのか
        - https://docs.python.org/ja/3/library/sys.html#sys.getsizeof
            > オブジェクトに直接起因するメモリ消費のみを表し、参照するオブジェクトは含みません。
        ```
        >>> A = 1
        >>> sys.getsizeof(A)
        28
        
        >>> A = 0.1
        >>> sys.getsizeof(A)
        24
        
        >>> A = ''
        >>> sys.getsizeof(A)
        41

        >>> A = list()
        >>> sys.getsizeof(A)
        56

        >>> A = set()
        >>> sys.getsizeof(A)
        216
        
        >>> A = dict()
        >>> sys.getsizeof(A)
        64
        
        >>> A = "a" * 1000
        >>> sys.getsizeof(A)
        1041

        >>> A = []
        >>> for i in range(1000):
        ...     A.append(i)
        >>> sys.getsizeof(A)
        8856
        
        >>> A = set()
        >>> for i in range(1000):
        ...     A.add(i)
        >>> sys.getsizeof(A)
        32984
        
        >>> A = dict()
        >>> for i in range(1000):
        ...     A[i] = A.get(i, 0) + i
        >>> sys.getsizeof(A)
        36952
        
        ```
        - 64-bit CPython だとして，参照が 8byte なのでリストは 8KB くらいになるのか
        - ハッシュテーブルをもつ dict, set はオブジェクトへの参照だけで list と比較すると定数倍メモリ消費量が多くなる．
            - 参照先のオブジェクト自体のメモリ消費が少ないと差が出るのか．

- 配列のインデックスの命名が長すぎても冗長になる
    - https://github.com/h1rosaka/arai60/pull/14#discussion_r2143457418
        > はい、変数名は長ければいいというものではないのです。


## 2-1
- 3 + set を使ったもの
    - これが一番手作業でおこなったときに自然な解法だった．

時間計算量 O（klogk） 空間計算量O(k)

```py
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if not 1 <= k <= len(nums1) * len(nums2):
            raise ValueError("k is out of range")
        
        if not nums1 or not nums2:
            return []

        def _is_qualified_pair(i: int, j: int) -> bool:
            if i >= len(nums1) or j >= len(nums2):
                return False            
            if i == 0 or j == 0:
                return True

            return (i - 1, j) in k_smallest_index_pairs and (i, j - 1) in k_smallest_index_pairs

        def _update_candidates(i: int, j: int) -> None:
            if _is_qualified_pair(i, j):
                heapq.heappush(candidates, (nums1[i] + nums2[j], i, j))

        k_smallest_index_pairs = set()
        candidates = []
        heapq.heappush(candidates, (nums1[0] + nums2[0], 0, 0))

        for _ in range(k):
            _, i, j = heapq.heappop(candidates)
            k_smallest_index_pairs.add((i, j))
            _update_candidates(i + 1, j)
            _update_candidates(i, j + 1)
        
        return [[nums1[i], nums2[j]] for i, j in list(k_smallest_index_pairs)]
```


## 2-2
- 3 + list で解く方法
- candidates だと情報少なすぎるかもかと思ったが，逆に candidate_sum_index_pairs だと冗長すぎると感じる．
- ref
    - https://github.com/TORUS0818/leetcode/pull/12#discussion_r1623354548
    - これのlist https://github.com/Yoshiki-Iwasa/Arai60/pull/9#discussion_r1647019606
        > これを実現するためには、set を使うか、そうでなければ、「i 列目は次何を出すかを入れる配列、j 行目は次何を出すかを入れる配列をそれぞれ用意して、両方とも次に出すやつだったら (i, j) 出せますね。」
    - https://github.com/ksaito0629/leetcode_arai60/pull/6/changes#diff-03a3fb8f1a7b3e86fadbacaa3d5f79bc6125150ee17fbc4bee45a9be9b1a5921R89

時間計算量 O（klogk） 空間計算量O(max(k, M, N))

```py
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if not 1 <= k <= len(nums1) * len(nums2):
            raise ValueError("k is out of range")
        
        if not nums1 or not nums2:
            return []

        def _update_candidates(i: int, j: int) -> None:
            if i >= len(nums1) or j >= len(nums2):
                return 
            if (i, j) != (i_next_to_smallest[j], j_next_to_smallest[i]):
                return 
            
            heapq.heappush(candidate_sum_index_pairs (nums1[i] + nums2[j], i, j))    
            
        k_smallest_pairs = []
        candidate_sum_index_pairs = []
        heapq.heappush(candidate_sum_index_pairs, (nums1[0] + nums2[0], 0, 0))
        
        i_next_to_smallest = [0] * len(nums2)
        j_next_to_smallest = [0] * len(nums1)

        for _ in range(k):
            _, i, j = heapq.heappop(candidate_sum_index_pairs)
            k_smallest_pairs.append([nums1[i], nums2[j]])
            i_next_to_smallest[j] += 1
            j_next_to_smallest[i] += 1
            _update_candidates(i + 1, j)
            _update_candidates(i, j + 1)

        return k_smallest_pairs
```


## 2-3
- 1 の訪問済み状態を追加で管理する
- こちらの (i + 1, j), (i, j + 1) をとりあえず追加．あとで困るから訪問管理するか，の方が自然に思えるべきか
- 初期化も共通化できる
    https://github.com/h1rosaka/arai60/pull/14#discussion_r2143513364

- `for _ in range(k):` について
    - 最初で k の範囲がない場合にエラーを挙げるようにして heap がなくならない保証をしていると考えたが，k が大きい場合にエラーが帰ってくるのは直感的じゃないな．
    - https://discord.com/channels/1084280443945353267/1201211204547383386/1206515949579145216
        > 一方で、ペアが K 個なかったら、あるだけ全部小さい順に返すというのは許されたフォールバックでしょう。(たとえば、ウェブページで、項目を100件表示のときにはうまく表示されていたのに、1000件表示とした途端に全部なくなったりするのは気持ちの悪い動作です。)
        > 別に、これはこういうふうに、面接の場でコードを書かなくてはいけないというよりは、書き終わった後に、こうした方が親切な気がするというお話ができればいいと思います。
        
    - 読み手にも優しくない．
        - いきなり読んだ人は for _ in range(k): _, i, j = heapq.heappop(candidates) で candidates 空の時どうすんだろ，って不安になるか
        - 書いている人は ロジックの性質上，candidates がなくなることがないのはわかるとしても．
        - while なら空 pop がないことはすぐ分かり，こちらが適していると感じた．

時間計算量 O（klogk） 空間計算量O(k)

```py
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if k < 0:
            raise ValueError("k is out of range")

        def _update_candidates_and_visited(i: int , j: int) -> None:
            if i >= len(nums1) or j >= len(nums2) or (i, j) in visited:
                return

            heapq.heappush(candidates, (nums1[i] + nums2[j], i, j))
            visited.add((i, j))

        k_smallest_pairs = []
        candidates = []
        visited = set()
        _update_candidates_and_visited(0, 0)

        while len(k_smallest_pairs) < k and not candidates:
            _, i, j = heapq.heappop(candidates)
            k_smallest_pairs.append([nums1[i], nums2[j]])
            _update_candidates_and_visited(i + 1, j)
            _update_candidates_and_visited(i, j + 1)
        
        return k_smallest_pairs
```


## 2-4
- 2 で解く
- 初期化の時点で最初に (0, j) 0<=j<len(nums2) を追加してしまう．あとは i を増やす方向だけに集中すればいい
    - https://github.com/n6o/leetcode_arai60/pull/10/changes#diff-39cc9cee3d79ce9bea375f8ce45d130675c35bfce12bf5471ba4a3b3dd897ad6R213
    - https://github.com/naoto-iwase/leetcode/pull/10/changes#diff-286fb5038eb6a401d524f96be2232f62da7edf98e92fb04003be9fcc29b20eb8R65

時間計算量 O（klogk） 空間計算量O(min(k, M))

```py
class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if k < 0:
            raise ValueError("k is out of range")

        k_smallest_pairs = []
        candidates = []

        for j in range(min(k, len(nums2))):
            heapq.heappush(candidates, (nums1[0] + nums2[j], 0, j))
        
        while len(k_smallest_pairs) < k and candidates:
            _, i, j = heapq.heappop(candidates)
            k_smallest_pairs.append([nums1[i], nums2[j]])
            if i + 1 >= len(nums1):
                continue
            
            heapq.heappush(candidates, (nums1[i + 1] + nums2[j], i + 1, j))
        
        return k_smallest_pairs
```
