## Elixir基礎 - 4

#### case, cond, if/unless, do/end
***
<br />
「Elixir/Phoenixで遊ぶ」と言いながら気が付いたら基礎だけで4回目。。。Elixirがっつりはまってます(笑)
<br />
<br />
色々試したい事が沢山ありますが、基礎は大事という事で、今回はElixirの`case`,`cond`,`if/unless`, `do/end`について。
<br />
<br />

> case

<br />
`case`は値を比較し、マッチした/マッチしなかった場合毎に処理を実行する。
マッチの条件を書く際に、特定の条件(guard)を条件に加える事ができる。<br />
書き方としては`when XXXX`で、XXXXに特定の条件を指定できる。
<br />

例)

```elixir
iex(1)> sample_case = fn arg ->
...(1)> case arg do
...(1)> [:ok, x, y] when x > 10 and is_boolean(y) ->
...(1)> "success."
...(1)> [:ok, x, y] when x <= 10 and is_boolean(y) ->
...(1)> "warning."
...(1)> [:ng, x, y] ->
...(1)> "error."
...(1)> _ ->
...(1)> "faild argument."
...(1)> end
...(1)> end
#Function<6.54118792/1 in :erl_eval.expr/5>

iex(2)> sample_case.([:ok, 20, true])
"success."
iex(3)> sample_case.([:ok, 10, true])
"warning."
iex(4)> sample_case.([:ng, 10, true])
"error."
iex(5)> sample_case.([1, 2, 3])      
"faild argument."
```

上の例では比較対象としてlistを想定している。<br />
* `[:ok, x, y] when x > 10 and is_boolean(y)`ではlistの1番目の要素が`:ok`、xが10より大きく、listの3番目の要素がboolean型であれば`success.`を出力する。<br />

* `[:ok, x, y] when x <= 10 and is_boolean(y)`ではlistの1番目の要素が`:ok`、xが10以下で、listの3番目の要素がboolean型であれば`warning.`を出力する。<br />
* `[:ng, x, y]`ではlistの1番目の要素が`:ng`で、listの2番目と3番目の要素が存在すれば`error.`を出力する。<br />

* `_ ->`では条件が一致しなかった全ての値に対して実行される。

<br />
`case`のように、他の言語と違ってパターンケースを直感的に書けるのが嬉しい所。
他の言語だとswitch 〜 caseの先でまたif文を書かないといけなくなる。

<br />

> cond

<br />
異なる条件で分岐を判定する。他言語の`elseif`にあたる。

例)
```elixir
iex(6)> sample_cond = fn x, y ->
...(6)> cond do
...(6)> x == 1 and y == 1 ->
...(6)> "x, y = 1"
...(6)> is_atom(x) or is_atom(y) ->
...(6)> "x or y atom"
...(6)> x * y == 9 ->
...(6)> "x * y = 9"
...(6)> end
...(6)> end
#Function<12.54118792/2 in :erl_eval.expr/5>
iex(7)> sample_cond.(1, 1)
"x, y = 1"
iex(8)> sample_cond.(1, :ok)
"x or y atom"
iex(9)> sample_cond.(3, 3)  
"x * y = 9"
```
上の例ではx, yに対して、異なる条件で判定している。
<br />
<br />

> if/unless

<br />
`if, unless`に関しては、`elseif`がないという事以外は他言語と同じ。

例)
```elixir
iex(10)> if true do
...(10)> "true"
...(10)> else
...(10)> "false"
...(10)> end
"true"
iex(12)> unless true do
...(12)> "true"
...(12)> else
...(12)> "false"
...(12)> end
"false"
```

<br />

> do/end

<br />
do〜endブロック。Elixirでは以下3種類のブロックの書き方がある。

* XXXX, do:

一行で書く場合。<br />
例)
```elixir
iex(14)> if true, do: "true"
"true"
```
<br />
* XXXX, do: ()

複数行で書く場合。<br />
例)
```elixir
iex(15)> if true, do: (
...(15)> x = "hoge"
...(15)> x <> "fuga"
...(15)> )
"hogefuga"
```
<br />
* XXXX do 〜 end

これも同じく複数行で書ける。<br />
例)
```elixir
iex(18)> if true do
...(18)> x = 1 + 3
...(18)> x + 10
...(18)> end
14
```
<br />
#### まとめ
***
<br />
`cond`が他の言語にはなく、特殊な感じですが慣れればサクッと書けそうな気がします。
個人的には`case`のパターンマッチ + guardが面白いと思いました。
コードがすごくスッキリしそう(笑)
