## ElixirでGitHubリポジトリ同期ツールの作成 1

GitHubのリポジトリと同期してくれるツールをElixirで作成<br />
既存のツールがありそうだけど、勉強を兼ねて作成してみます。


> やりたい事

* 自分のリポジトリ一覧を取得
* 取得したリポジトリ名でカレントディレクトリを検索
* リポジトリがなければclone、あればgitコマンドを実行
* gitコマンドは設定で変更可能

#### リポジトリ一覧の取得

[GitHub Api](https://developer.github.com/v3/)を使う<br />
httpsでリクエストを投げればJSON形式で情報が取れる

例) ユーザーの情報取得<br />
```
curl -i https://api.github.com/users/{ユーザーID}
```
※ -i を付けるとHTTPヘッダ情報も取れる

例)  ユーザーのリポジトリ一覧の取得<br />
```
curl https://api.github.com/users/{ユーザーID}/repos
```

#### 使用するライブラリ

Github APIを使用するので、HTTPクライアントとJSONパーサーが必要<br />
今回は以下のライブラリを使用します

* HTTPクライアントライブラリ [Httpotion](https://github.com/myfreeweb/httpotion)
* JSONライブラリ [posion](https://github.com/devinus/poison)

#### プロジェクトの作成

プロジェクト名を**「synchub」**として作成

```sh
$ mix new synchub
```

`mix.exs`を修正

```ruby
defmodule Synchub.Mixfile do
  use Mix.Project

  def project do
    [app: :synchub,
     version: "0.0.1",
     elixir: "~> 1.0",
     build_embedded: Mix.env == :prod,
     start_permanent: Mix.env == :prod,
     deps: deps]
  end

  def application do
    [applications: [:logger, :httpotion]] # httpotionを追加
  end

  defp deps do
    [
      {:ibrowse, github: "cmullaparthi/ibrowse", tag: "v4.1.2"},
      {:httpotion, "~> 2.1.0"}, # HTTPクライアントライブラリ
      {:poison, "~> 1.5"} # JSONを扱うライブラリ
    ]
  end
end
```

依存ライブラリの取り込み
```sh
$ mix deps.get
```

#### とりあえずリポジトリ一覧の表示

HttpotionのREADMEにGitHubApiを使った簡単なサンプルがあるので、<br />
それを少し修正して、APIの結果を表示するmoduelを作成します

* lib/synchub.ex

```ruby
defmodule Synchub do
  use HTTPotion.Base

  @apiurl "https://api.github.com/"

  def process_url(url) do
    @apiurl <> url
  end

  def process_request_headers(headers) do
    Dict.put headers, :"User-Agent", "{ユーザー名}※"
  end

  def process_response_body(body) do
    body |> Poison.decode! |> put_repo_name
  end

  def put_repo_name([info|tail]) do
    IO.puts info["name"]
    put_repo_name(tail)
  end

  def put_repo_name([]), do: nil
end
```
※ GithubApiではUser-Agentが必須、User-Agentがないと以下のようなエラーが出る
```
Request forbidden by administrative rules.
Please make sure your request has a User-Agent header
(http://developer.github.com/v3/#user-agent-required).
Check https://developer.github.com for other possible causes.
```
User-Agentにはユーザー名かアプリケーション名を入れとく

処理としては、以下でJSONをパースして`put_repo_name`関数へ渡している
```ruby
def process_response_body(body) do
  body |> Poison.decode! |> put_repo_name
end
```

`put_repo_name`関数でリポジトリ一覧のリストを再帰的に呼び出しながらキー"name"の値を表示
```ruby
def put_repo_name([info|tail]) do
  IO.puts info["name"]
  put_repo_name(tail) # リストの先頭要素(info)以外(tail)を引数に渡す
end

def put_repo_name([]), do: nil # リストが空の時に呼ばれる
```

実行してみる
```
$ iex -S mix
iex(1)> Synchub.get("users/{ユーザーID}/repos")
```

↑でリポジトリ一覧の情報が出力されればOK
