
#### Elixirのプロジェクト管理ツール
***

今回はElixirのプロジェクト管理ツールである`Mix`について。<br />
Rubyでいう所の`gem`に相当するもの

早速使ってみたいと思います。

↓以下Mixの主なコマンド

|コマンド|詳細|
|:------|:---|
|mix new|新規プロジェクトの作成|
|mix run|指定されたファイル又はプロジェクト実行|
|mix test|プロジェクトのテスト実行|
|mix deps.get|mix.exsで定義された依存パッケージの取得|
|iex -S mix|iexを起動し、プロジェクトを実行|

<br />

> 新規プロジェクト作成

Mixを使ってプロジェクトを作成すると以下のようになる。

```sh
$ mix new sample
* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/sample.ex
* creating test
* creating test/test_helper.exs
* creating test/sample_test.exs

Your mix project was created successfully.
You can use mix to compile it, test it, and more:

    cd sample
    mix test

Run mix help for more commands.
```

lib/sample.exにコードを書いていく。
<br />

> mix.exs

プロジェクトの設定ファイル。
↓プロジェクト作成直後の`mix.exs`

```elixir
defmodule Sample.Mixfile do
  use Mix.Project

  def project do
    [app: :sample,
     version: "0.0.1",
     elixir: "~> 1.0",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps]
  end

  # Configuration for the OTP application
  #
  # Type `mix help compile.app` for more information
  def application do
    [applications: [:logger]]
  end

  # Dependencies can be Hex packages:
  #
  #   {:mydep, "~> 0.3.0"}
  #
  # Or git/path repositories:
  #
  #   {:mydep, git: "https://github.com/elixir-lang/mydep.git", tag: "0.1.0"}
  #
  # Type `mix help deps` for more examples and options
  defp deps do
    []
  end
end
```

* `def project do 〜 end`

プロジェクトのバージョン設定やElixirのバージョンなどを設定する。

* `def application do 〜 end`

アプリケーションコンパイル時に必要なモジュールを定義する。

* `defp deps do 〜 end`

プロジェクトが依存するパッケージを定義する。

> mixで実行

先ほどサンプルで作った`lib/sample.ex`を以下に編集

```elixir
defmodule Sample do

def list([t|h]) do
  IO.puts(t)
  list(h)
end

def list([]), do: nil
end
```

コンパイル

```sh
$ mix compile

```

iexから実行してみる

```sh
$ iex -S mix
rlang/OTP 18 [erts-7.0] [source] [64-bit] [async-threads:10] [hipe] [kernel-poll:false]

Interactive Elixir (1.0.5) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> Sample.list([1, 2, 3, 4, 5])
1
2
3
4
5
nil
iex(2)>
```
lib/sample.exで定義した`list/1`を実行し、リストの内容を表示している。
<br />

> テストの実行

作成したプロジェクトに`test`ディレクトリが作成されており、
中に、`sample_test.exs`と`test_helper.exs`が作成されている。
テストは`sample_test.exs`に書く

`sample_test.exs`を以下に編集

```elixir
defmodule SampleTest do
  use ExUnit.Case

  test "list" do
    assert Sample.list([]) == nil
  end
end
```

空のリストの引数に対してnilを返すかのテスト

テストを実行してみる<br />
`mix test`でテスト実行、`mix test --trace`でテストの
詳細を表示してテストを実行する

* mix test

```sh
$ mix test
Compiled lib/sample.ex
Generated sample app
.

Finished in 0.08 seconds (0.08s on load, 0.00s on tests)
1 tests, 0 failures

Randomized with seed 212541
```

* mix test --trace

```sh
$ mix test --trace
Compiled lib/sample.ex
Generated sample app
.

SampleTest
  * list (0.00ms)


Finished in 0.06 seconds (0.06s on load, 0.00s on tests)
1 tests, 0 failures

Randomized with seed 904189
```

* テスト失敗時

`assert Sample.list([]) == nil`を`assert Sample.list([]) == 0`
に変更

```sh
$ mix test --trace

SampleTest
  * list (9.2ms)

  1) test list (SampleTest)
     test/sample_test.exs:4
     Assertion with == failed
     code: Sample.list([]) == 0
     lhs:  nil
     rhs:  0
     stacktrace:
       test/sample_test.exs:5



Finished in 0.08 seconds (0.07s on load, 0.01s on tests)
1 tests, 1 failures

Randomized with seed 363937
```
<br />

> mix.exsにライブラリを指定

例として、JSONのライブラリである[posion](https://github.com/devinus/poison)を使ってみる。

* mix.exsを以下に編集

```elixir
defmodule Sample.Mixfile do
  〜 省略 〜
  defp deps do
    [{:poison, "~> 1.5"}]
  end
end
```

* プロジェクトの依存関係を更新

```sh
$ mix deps.get
```

* ライブラリを使う
`lib/sample.ex`を以下に編集

```elixir
defmodule Sample do

  @derive [Poison.Encoder]
  defstruct [:name, :age]

def list([t|h]) do
  IO.puts(t)
  list(h)
end

def list([]), do: nil

end
```

* 実行してみる

```sh
$ iex -S mix
iex(1)> Poison.encode!(%Sample{name: "Hoge", age: 20})        
"{\"name\":\"Hoge\",\"age\":20}"
iex(2)> Poison.decode!(~s({"name": "Fuga", "age": 30}), as: Sample)        
%Sample{age: 30, name: "Fuga"}
iex(3)>
```

↑では、エンコードとデコードを行っている
<br />

> まとめ

ruby同様に依存関係の管理や、テストなど使いやすいように感じました。<br />
個人的には`.gitignore`や`README.md`も作ってくれるので便利だと思います
