## Elixir基礎 - 1

#### ちゃんとElixirを学ぶ
***
Phoenixを少し触ってみたので、ちゃんとElixirを学んでみる。

* **まずは実行方法**

[以前](http://developabout0309.blogspot.jp/2015/07/elixirphoenix-1.html)インタラティクモード`iex`で軽く動作させてみましたが、<br />
ファイルにコードを書いて実行させてみます。

```elixir
IO.puts "Hello Elixir!!"
```
↑のコードを書いたファイルを`sample.exs`として保存<br />
ターミナル上で以下のように実行できる。
```
$ elixir sample.exs
Hello Elixir!!
```
------------------
* **型**

Elixirの型としては以下

1. integer
* float
* boolean
* atom / symbol
* string
* list
* tuple

integerとfloatは後々触れるとして、まずは・・・

> boolean

trueとfalseの二つを値として持つ。<br />
Elixirでは型チェックメソッドとして`is_boolean`が使える

例)
```
iex(1)> is_boolean(false)
true
iex(2)> is_boolean(1)
false
iex(3)> is_boolean(1 == 1)
true
```

> atom

rubyや他言語でいうところのsymbol<br />
boleanの値はatom値と等価
```
iex(1)> true == :true
true
iex(2)> true == :false
false
iex(3)> is_atom(true)
true
iex(4)> is_atom(:true)
true
```
atomでも型チェックメソッドとして`is_atom`が使える

> string

ダブルクオーテーション`""`で囲む事でstring型として使用できる。<br />
文字コードとしては`UTF-8`。ダブルクオーテーションの文字列中に変数を挿入できる。

例)
```
iex(1)> "slowhand"
"slowhand"
iex(2)> name = "slowhand"
"slowhand"
iex(3)> "hello #{name}"
"hello slowhand"
```

上でも出てきたIOモジュールを使って出力してみる。
```
iex(4)> IO.puts "hi\n#{name}"
hi
slowhand
:ok
```

IO.putsは戻り値としてatom値の`:ok`を返す。<br />
文字列長を取得するには、`String.length`を使う。<br />
また、バイト数を求めるには`byte_size`を使う。

例)
```
iex(5)> byte_size("abcあいう")
12
iex(6)> byte_size("あ")
3
iex(7)> String.length("abcあいう")
6
iex(8)> String.length("あ")
1
```

> list

`[]`で囲む事でlist型となり、様々な型を挿入できる。

```
iex(9)> [1,true,"a",:ok]
[1, true, "a", :ok]
```
また`++`、`--`演算子を使う事でlistの連結や差し引いたりできます。

```
iex(10)> [1,2,3] ++ [4,5,6]
[1, 2, 3, 4, 5, 6]
iex(11)> [10,9,8,7,6,5,4] -- [9, 7, 5]
[10, 8, 6, 4]
```

`hd`と`tl`メソッドを使う事でlistの先頭要素と残りの要素を取得する事ができます。<br />
ちなみに空のlistを渡すとエラーになります。
例)
```
iex(12)> list = [1,"a",:b,3]
[1, "a", :b, 3]
iex(13)> hd(list)
1
iex(14)> tl(list)
["a", :b, 3]
iex(15)> hd[]
** (SyntaxError) iex:15: syntax error before: ']'
```

※たまに数値のlistを作成するとシングルクオーテーションで囲んだ文字が出力される。

例)
```
iex(15)> [104, 101, 108, 108, 111]
'hello'
```
どうやらこれはElixirが印刷可能なASCIIコードを見ると文字として出力するらしい。<br />
ちなみにシングルクオーテーションで囲まれた文字列とダブルクオーテーションで囲まれた文字列を<br />
Elixirでは違うものとしてみる。

> tuple

`{}`で囲む事でtuple型となる。
要素を追加した順番にインデックスが割り振られ、先頭の要素はインデックス0でアクセスできる。

例)
```
iex(16)> t =  {:ok, :ng}
{:ok, :ng}
iex(17)> elem(t, 0)
:ok
iex(18)> is_tuple(t)
true
iex(19)> tuple_size(t)
2
```
また、`put_elem`を使う事でtuple値に要素を追加し、新たなtuple値として返却します。<br />
というのも、Elixirでは不変データ構造をもっている為。

今回はここまで。
