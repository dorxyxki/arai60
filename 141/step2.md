# 他の方の解法やディスコのログを見て解く


- そもそも id ではなくインスタンスを直接 set の要素として扱う方が良さそう
    - 過去PR: https://github.com/komdoroid/arai60/pull/8/changes
    - 集合の要素：
        - https://docs.python.org/ja/3/library/stdtypes.html#set
    - hashable なオブジェクト：
        - https://docs.python.org/ja/3/glossary.html#term-hashable
            > ユーザー定義のクラスのインスタンスであるようなオブジェクトはデフォルトでハッシュ可能です。 それらは全て (自身を除いて) 比較結果は非等価であり、ハッシュ値は id() より得られます。

- オブジェクトの同一性を判定する際は is が用意されているためそちらがよさそう
    - 過去PR: https://github.com/kitano-kazuki/leetcode/pull/1/changes#r2787203945
    - https://docs.python.org/3/reference/datamodel.html#object.__eq__
        > By default, object implements __eq__() by using is, returning NotImplemented in the case of a false comparison: True if x is y else NotImplemented.
    - https://docs.python.org/ja/3/reference/expressions.html#is
        > 演算子 is および is not は、オブジェクトの同一性に対するテストを行います: x is y は、 x と y が同じオブジェクトを指すとき、かつそのときに限り真になります。 オブジェクトの同一性は id() 関数を使って判定されます。
    - https://docs.python.org/ja/3/reference/datamodel.html
        > すべてのオブジェクトは、同一性 (identity)、型、値をもっています。 同一性 は生成されたあとは変更されません。これはオブジェクトのアドレスのようなものだと考えられるかもしれません。 is 演算子は2つのオブジェクトの同一性を比較します。 id() 関数は同一性を表す整数を返します。
    - https://docs.python.org/ja/3/reference/expressions.html#value-comparisons
        > 等価比較 (== および !=) のデフォルトの振る舞いは、オブジェクトの同一性に基づいています。 従って、同一のインスタンスの等価比較の結果は等しいとなり、同一でないインスタンスの等価比較の結果は等しくないとなります。 デフォルトの振る舞いをこのようにしたのは、全てのオブジェクトを反射的 (reflexive つまり x is y ならば x == y) なものにしたかったからです。
    
- None の判定についてstyleguideでは `A is not None` こう言った書き方が推奨されている．
    - https://google.github.io/styleguide/pyguide#214-truefalse-evaluations

- 名前の空間とオブジェクトの空間について
    - https://github.com/mamo3gr/arai60/pull/1/changes#r2621551696
    - https://discord.com/channels/1084280443945353267/1226508154833993788/1227291990987772015
    - https://discord.com/channels/1084280443945353267/1196472827457589338/1200081854301225011
    - 代入という操作が右辺で評価されたオブジェクトに左辺の変数を束縛するということ

- 二つのポインタを使ってのサイクル検出は当たり前の知識ではないらしい

## 2-1
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        node = head
        visited = set()

        while node is not None:
            if node in visited:
                return True

            visited.add(node)
            node = node.next

        return False
```
