
#### ElixirでRedis
***

今回はElixir志向を変えて、ElixirでRedisを操作してみたいと思います<br />
ElixirのRedisを扱うライブラリとして今回は[exredis](https://github.com/artemeff/exredis)を使用


<br />
> Redisの設定

インストール方法は[以前の記事](http://developabout0309.blogspot.jp/2015/05/3.html)を参照<br />
今回は扱いやすいようにシンボリックリンクを張って`.bash_profile`を<br />
ちょこっと修正<br />

* シンボリックリンクの作成

```sh
$ ln -s インストールディレクトリ/redis-X.X.X /usr/bin/redis
```

* `.bash_profile`に以下を追加

```sh
PATH=$PATH:/usr/bin/redis/src

export PATH

alias rediss='redis-server'
alias redisc='redis-cli'
```

`rediss`でRedisサーバー起動, `redisc`でクライアント起動

<br />

> プロジェクト作成 & exredisインストール

`rediscli`の名前でプロジェクト作成

```sh
$ mix new rediscli
* creating README.md
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/rediscli.ex
* creating test
* creating test/test_helper.exs
* creating test/rediscli_test.exs

Your mix project was created successfully.
You can use mix to compile it, test it, and more:

    cd rediscli
    mix test

Run `mix help` for more commands.
```

`mix.exs`にexredisをプロジェクトに取り込むように編集

```elixir
defmodule Rediscli.Mixfile do
  use Mix.Project
  〜 省略 〜
  defp deps do
    [{:exredis, ">= 0.2.1"}]
  end
end
```

プロジェクトに適用

```sh
$mix deps.get
```

`iex`で確認してみる

```sh
$ iex -S mix

iex(1)> client = Exredis.start
#PID<0.174.0>
iex(2)> {:ok, client} = Exredis.start_link —> パターンマッチング {:OKの場合しかclientにインスタンスが入らない
{:ok, #PID<0.178.0>}
iex(3)> client |> Exredis.query ["SET", "name", "slowhand"]
"OK"
iex(4)> client |> Exredis.query ["GET", "name"]
"slowhand"
iex(5)> client |> Exredis.stop
:ok
```

↑ではデフォルトの設定で起動(host:127.0.0.1, port:6379)<br />
起動したのちpipe operator`|>`を使ってExredis.queryにclientを渡している<br />
queryではredisのコマンドを実行している

現時点でkey`name`に`slowhand`が格納されている<br />
今度は実際にredisのクライアントから確認

```sh
$ redisc
127.0.0.1:6379> get name
"slowhand"
```
ちゃんと`slowhand`が取れています
今度は逆に、redisのクライアントから設定した値をelixirから参照

```sh
$ redisc
127.0.0.1:6379> set name fuge
OK
127.0.0.1:6379> get name
"fuge"
127.0.0.1:6379> exit

$ iex -S mix
iex(1)> {:ok, client} = Exredis.start_link
{:ok, #PID<0.114.0>}
iex(2)> client |> Exredis.query ["GET", "name"]
"fuge"
iex(3)> client |> Exredis.stop
:ok
iex(4)>
```

ちゃんとredisのクライアントから設定した値をexredisでみれています<br />
とりあえず今回はここまで
