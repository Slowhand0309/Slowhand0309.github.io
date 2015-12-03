
#### ElixirのBinariesとbitstrings
***

今回はElixirでのバイトとビットの扱いについて<br />
Elixirでバイトを扱う場合は`<< >>`を使います

<br />
> サンプル

基本的な使い方。

```sh
iex(1)> <<0, 1, 2, 3>>
<<0, 1, 2, 3>>
iex(2)> byte_size <<1, 2, 3, 4, 5>>
5
iex(3)> <<1, 78>> <> <<24, 68>> # バイト同士の連結
<<1, 78, 24, 68>>
iex(4)> "abcd" <> <<0>> # 文字列とバイトの連結
<<97, 98, 99, 100, 0>>
iex(5)> "あいう" <> <<0>> # 文字列とバイトの連結
<<227, 129, 130, 227, 129, 132, 227, 129, 134, 0>>
iex(6)>
```

バイトのサイズを知りたい時は`byte_size/1`を使います<br />
バイトの連結に関しては文字列と同じように`<>`演算子を使います<br />

<br />

> バイトサイズ超過

1バイト(0〜255)超過したものについては切り取られるか<br />
指定された形式に変換できる

```sh
iex(1)> <<255, 256, 257, 258>> # 256以上は切り取り
<<255, 0, 1, 2>>
iex(2)> <<500 :: size(16)>> # 2byte(16bit)で表示する
<<1, 244>>
iex(3)> <<256 :: utf8>> # utf8で表示する
"Ā"
```

> ビット

Elixirでビットを扱うには`<<>>`を使いますが、sizeが1のものになります

```sh
iex(1)> <<1>> # 1バイト
<<1>>
iex(2)> <<1 :: size(1)>> # 1ビット
<<1::size(1)>>
iex(3)> is_binary(<<1>>) # バイトか?
true
iex(4)> is_binary(<<1 :: size(1)>>) # バイトか? -> ビット
false
iex(5)> is_bitstring(<<1>>) # ビットか? -> 8ビット
true
iex(6)> is_bitstring(<<1 :: size(1)>>) # ビットか? -> 1ビット
true
iex(7)> bit_size(<<1>>)
8
iex(8)> bit_size(<<1 :: size(1)>>)
1
```
<br />
`is_binary`と`is_bitstring`が若干間違えそうな感じですが<br />
ビット(size(1))に対しての`is_binary`は`false`を返し<br />
バイト(size指定なし)に対しての`is_bitstring`は`true`を返します<br />

※ちなみに`<<X :: size(1)>>`のXが1より大きい場合は切り取られます

> パターンマッチ

バイト/ビットでもパターンマッチが可能です<br />

```sh
iex(1)> binary_case = fn arg ->
...(1)> case arg do
...(1)> <<1, 2, x>> -> # 3バイトのパターンにマッチ
...(1)> "match <<1, 2, x>>"
...(1)> <<1, 2, x :: binary>> -> # xが残りのバイトとマッチ
...(1)> "match <<1, 2, x :: binary>>"
...(1)> <<0 :: size(1)>> -> # ビット
...(1)> "match <<0 :: size(1)>>"
...(1)> _ ->
...(1)> "no match"
...(1)> end
...(1)> end
#Function<6.54118792/1 in :erl_eval.expr/5>
iex(2)> binary_case.(<<1, 2, 3>>)
"match <<1, 2, x>>"
iex(3)> binary_case.(<<1, 2, 3, 4>>)
"match <<1, 2, x :: binary>>"
iex(4)> binary_case.(<<1, 2, 3, 4, 5>>)
"match <<1, 2, x :: binary>>"
iex(5)> binary_case.(<<0 :: size(1)>>)
"match <<0 :: size(1)>>"
iex(6)> binary_case.(<<1 :: size(1)>>)
"no match"
```
