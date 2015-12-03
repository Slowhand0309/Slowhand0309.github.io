## Phoenix controllerに値を渡す

#### メソッド&ページ追加
***
[前回](http://developabout0309.blogspot.jp/2015/08/elixirphoenix-3.html)で作成したプロジェクトを使って、URLで指定した値を画面に表示させる。<br />
例えば、`http://localhost:4000/hello/Slowhand`にアクセスすると`hello Slowhand.`と表示するようにしてみる

* routeに新規パス追加

```
defmodule SamplePhoenix.Router do
  use SamplePhoenix.Web, :router

  pipeline :browser do
    plug :accepts, ["html"]
    plug :fetch_session
    plug :fetch_flash
    plug :protect_from_forgery
  end

  pipeline :api do
    plug :accepts, ["json"]
  end

  scope "/", SamplePhoenix do
    pipe_through :browser # Use the default browser stack

    get "/", PageController, :index
    get "/hello", HelloController, :index
    get "/hello/:messenger", HelloController, :show ☆
  end

  # Other scopes may use custom stacks.
  # scope "/api", SamplePhoenix do
  #   pipe_through :api
  # end
end
```
前回同様`web/router.ex`の`scope "/"`ブロックに追加。<br />
`/hello/:messenger`は[Dict](http://elixir-lang.org/docs/stable/elixir/Dict.html)というKey,Valueを提供するmoduleを使って<br />
controllerから値を取れるようにURLにキー(:messager)をくっつけてます。

* コントローラにメソッド追加

`web/controllers/hello_controller.ex`を以下内容に修正する。

```elixir
defmodule SamplePhoenix.HelloController do
  use SamplePhoenix.Web, :controller

  plug :action

  def index(conn, _params) do
    render conn, "index.html"
  end

  def show(conn, %{"messenger" => messenger}) do
    render conn, "show.html", messenger: messenger
  end
end
```

`%{"messenger" => messenger}`でkey'messenger'をmessengerに設定しています。<br />
また、他のparamsも使用したい場合は以下のようにすることも可能。

```elixir
def show(conn, %{"messenger" => messenger} = params) do
  ...
end
```

* テンプレートの作成

`web/templates/hello/show.html.eex`を以下内容で作成する

```
<div class="jumbotron">
  <h2>Hello <%= @messenger %>!</h2>
</div>
```

サーバを起動し`http://localhost:4000/hello/Slowhand`にアクセス

上記の画面が表示される
