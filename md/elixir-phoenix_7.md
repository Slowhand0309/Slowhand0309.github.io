## Elixir基礎 - 3

#### マッチ演算子
***

Elixirでは`=`で左辺の変数に右辺の値を代入するが、<br />
`=`は代入とは別に**マッチ演算子**としても使用できる。

```
iex(1)> a = 1
1
iex(2)> a
1
iex(3)> 1 = a
1
iex(4)> a = 1
1
iex(5)> a = 2
2
iex(6)> 2 = a
2
iex(7)> 3 = a
** (MatchError) no match of right hand side value: 2
```
上の例でも分かるように左辺が変数でない場合、左辺と右辺を比較し<br />
異なる場合例外を発生させる。

#### パターンマッチ
***

マッチ演算子はより複雑なパターンを比較する事ができる。

```
iex(15)> {:ok, status} = {:ok, 200}
{:ok, 200}
iex(16)> {:ok, status} = {:ng, 400}
** (MatchError) no match of right hand side value: {:ng, 400}
```
上の例はtuple型の最初の要素が`:ok`で始まっているもの以外は例外を発生させます。
<br />型やサイズが異なっても例外が発生します。
```
iex(16)> [a, b, c] = [1, 2]
** (MatchError) no match of right hand side value: [1, 2]

iex(16)> [a, b, c] = 1
** (MatchError) no match of right hand side value: 1
```
リストではheadとtailにマッチングさせる事もできます。
```
iex(16)> [h|t] = [1, 2, 3]
[1, 2, 3]
iex(17)> h
1
iex(18)> t
[2, 3]
iex(19)> [h|[:ok|t]] = [a, :ok, 1, 2]
["a", :ok, 1, 2]
iex(20)> h
"a"
iex(21)> t
[1, 2]
```
パターンマッチおもしろい^^
