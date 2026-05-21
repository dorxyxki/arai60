# 他の方の解法やディスコのログを見て解く

## 別の解法
- queue に積む時に反転させる方法(appendleft)
    - https://github.com/Satorien/LeetCode/pull/27/changes#diff-70cd15aac49da7b609181e0ea5e4fba6a8c687d9669b43b03cfe084df09886c4R70

## 参考
- フラグか深さの偶奇か
    - https://github.com/nanae772/leetcode-arai60/pull/27#discussion_r2402154745
        > 個人的にはrootをはじめとして降りるごとに反転していった結果、反転するのがlevelが奇数である時に合致した、という印象を持っています。
        > つまりlevelの偶奇で判断するのは要件から離れていると感じるので、levelが上がるにつれてis_left_to_rightのbool値を反転させていく方が好みです。
    - 結果として偶奇で判定できるというのは本質と感じずらい人も

- left_to_right 
    - https://github.com/hemispherium/LeetCode_Arai60/pull/26/changes#r3109696708
    
    - is_left_to_right との感じ方の違い
        - https://github.com/Manato110/LeetCode-arai60/pull/27/changes#r3116756507
            > 識別子が動詞の原形または命令形で書かれていると、メソッド名のように見える場合があります。 left_to_right はいかがでしょうか？

- reverse に関する話
    - https://github.com/tom4649/Coding/pull/26/changes#diff-e54ec50567667f700f911ca09b0293e94a84bd89f11b2b15f9baf5c4ee828b23R5
        > - `xs.reverse()`：破壊的（in-place）。リスト自体を反転して良いなら最有力
        > - `reversed(xs)`：非破壊の逆順イテレータ。`for` で回すだけならこれが軽い（コピー不要）
        > - `list(reversed(xs))` / `xs[::-1]`：**非破壊**で「反転した新しいリスト」が欲しいとき（どちらも \(O(n)\) コピー）
        > - 手動で `for i in range(len(xs)-1, -1, -1)`：特殊な制御が必要なときだけ（可読性は落ちがち）
    - 整理されていてわかりやすい
    
    - reversed
        - https://docs.python.org/3/library/functions.html#reversed
    - sequence.reverse()
        - https://docs.python.org/3/library/stdtypes.html#list.reverse
    - [::-1]とreversed の違いなど
        - https://stackoverflow.com/questions/37637829/difference-between-reverse-and-1


## 2-1
- 1-1 のリファクタ

```py
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []

        zigzag_values_by_level = []
        frontier = [root]
        left_to_right = True
        
        while frontier:
            next_frontier = []
            values = []
                    
            for node in frontier:
                values.append(node.val)
                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)
                
            if not left_to_right:
                values.reverse()
            zigzag_values_by_level.append(values)

            frontier = next_frontier
            left_to_right = not left_to_right

        return zigzag_values_by_level        
```

## 2-2
- 積む時に反転させる(appendleft)
    - list に戻す計算量がかかるので変わらないか

```py
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return None
        
        zigzag_values_by_level = []
        frontier = [root]
        left_to_right = True

        while frontier:
            values = deque([])
            next_frontier = []

            for node in frontier:
                if left_to_right:
                    values.append(node.val)
                else:
                    values.appendleft(node.val)

                if node.left is not None:
                    next_frontier.append(node.left)
                if node.right is not None:
                    next_frontier.append(node.right)
            
            zigzag_values_by_level.append(list(values))
            frontier = next_frontier
            left_to_right = not left_to_right

        return zigzag_values_by_level
```