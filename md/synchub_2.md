## ElixirでGitHubリポジトリ同期ツールの作成 2

[前回](http://developabout0309.blogspot.jp/2015/11/elixirphoenix-12-elixirgithub.html)に続いて同期ツールの作成<br />


#### config/config.exsの使用
***

前回GitHubApiを使ってリポジトリの一覧を取得した際にユーザー名や<br />
リクエストURLをソースコード上に直で書いていましたが、今回は`config/config.exs`<br />
を読み込んで使ってみたいと思います。

まずは`config/config.exs`に以下内容を追加

```ruby
config :synchub,
  apiurl: "https://api.github.com/",
  userid: "{ユーザーID}",
  username: "{ユーザー名}"
```
※{ユーザーID}/{ユーザー名}は使用するアカウントのユーザー名、ユーザーIDを設定して下さい

lib/synchub.exを修正

```ruby
defmodule Synchub do
  @moduledoc """
  The module of sync github repos.
  """
  use HTTPotion.Base

  @apiurl Application.get_env(:synchub, :apiurl) # (1)ここ!

  @doc "handle create url."
  def process_url(url) do
    @apiurl <> url
  end

  @doc "handle create request header."
  def process_request_headers(headers) do
    Dict.put headers, :"User-Agent", Application.get_env(:synchub, :username) # (2)ここ!
  end

  @doc "handle response of html body."
  def process_response_body(body) do
    body |> Poison.decode! |> put_repo_name
  end

  @doc "put repos name."
  def put_repo_name([info|tail]) do
    IO.puts info["name"]
    put_repo_name(tail)
  end

  def put_repo_name([]), do: nil
end
```

`config/config.exs`で設定した値を読み込む時は`Application.get_env`を使う<br />
↑では(1)と(2)でそれぞれ`apiurl`と`username`を読み込んでいる

#### カスタムタスクの作成
***

前回は以下のように実行していましたが、
```
$ iex -S mix
iex(1)> Synchub.get("users/{ユーザーID}/repos")
```
一回一回iexを起動するのも面倒くさいので、mixのタスクを作成してみたいと思います。
作成したいタスクとしては・・・
```
$ mix list.github.repos
```
とすると、`config/config.exs`で設定されたユーザーのリポジトリ一覧を取得できたら良さげ

* タスクファイルの作成

`lib/mix/tasks/list.github.repos.ex`を作成し、以下内容に編集する

```ruby
defmodule Mix.Tasks.List.Github.Repos do
  use Mix.Task

  @shortdoc "Show github repos list" # (1)

  def run(args) do
    url = "users/" <> Application.get_env(:synchub, :userid) <> "/repos" # (2)
    Synchub.start
    Synchub.get(url)
  end
end
```

モジュール名は作成したファイル名と対応する様な名前で作成<br />
(1) `@shortdoc`を書く事で`mix help`の一覧に説明が表示される<br />
(2) ここで`config/config.exs`から`userid`を読み込みurlを作成

* コンパイル

```
$ mix compile
```

`mix help`で一覧に表示されているか確認してみる

```
$ mix help
・・・
mix list.github.repos # Show github repos list
・・・
```
↑の様に表示されればOK

* 実行

```
$ mix list.github.repos
```
前回と同じ様にリポジトリの一覧が表示されればOKです
