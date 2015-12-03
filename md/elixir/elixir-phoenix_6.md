## Elixir基礎 - 2

#### 前回に続きElixirをちゃんと学ぶ
***

今回は、演算子。

* **算術演算**

Elixirでは算術演算子として`+. -. *. /`がある。<br />
それぞれ、加算、減算、乗算、除算。

```
iex(1)> 1 + 2
3
iex(2)> 5 - 3
2
iex(3)> 2 * 5
10
iex(4)> 10 / 2
5.0
```
上の結果を見ても分かる通り、除算の戻り値は常にfloat型になる。<br />
もしinteger型の必要であれば`div, rem`関数が使える。

```
iex(5)> div(10, 2)
5
iex(6)> div 9, 3
3
iex(7)> rem 10, 1
0
iex(8)> rem 10, 3
1
```
`rem`は割った余りを取得する。
関数呼び出しのかっこ`)`は書かなくても良い。<br />
他に、↓ 2進数、8進数、16進数の書き方

```
iex(1)> 0b001010
10
iex(2)> 0o1234
668
iex(3)> 0xff
255
```

#### 主な演算子
***

* **文字列連結**

> <>

文字列を連結させる。
```
iex(4)> "abc" <> "def"
"abcdef"
iex(5)> var = "ABC"
"ABC"
iex(6)> var <> "DEF"
"ABCDEF"
```

* **論理演算**

> and, or, not (&&, || !)

論理積(and, &&)、論理和(or ||)、否定(not, !)
```
iex(8)> is_atom(:a) and true
true
iex(9)> is_atom("a") and true
false
iex(10)> is_atom("a") or true
true
iex(11)> !is_atom("a")
true
iex(12)> true && false
false
iex(13)> true || false
true
iex(14)> !true
false
```
boolean型でない型で使用した場合、例外が発生する。
```
iex(15)> 1 and true
** (ArgumentError) argument error: 1
```

* **比較演算**

> ==, !=, ===, !==, <=, >=, <, >

`==`と`===`の違いは`==`は値の比較に対して、`===`は値のチェックに加え、<br />
型のチェックを行う。

```
iex(18)> 10 == 10.0
true
iex(19)> 10 === 10.0
false
iex(20)> 10.0 === 20.0
false
```
