## Phoenix ページ追加

#### 静的ページ追加
***
[前回](http://developabout0309.blogspot.jp/2015/07/elixirphoenix-2.html)で作成したプロジェクトを使って公式サイトの[ドキュメント](http://www.phoenixframework.org/docs/adding-pages)を参考に静的ページを追加してみる

* プロジェクトのディレクトリ構造

```
|--_build
|--config
|--deps
|--lib
|--priv
|--test
|--node_modules
|--web
|  |--channels
|  |--controllers
|  |  |--page_controller.ex
|  |--models
|  |--router.ex
|  |--static
|  |  |--css
|  |  |  |--app.scss
|  |  |--js
|  |  |  |--app.js
|  |  |--vendor
|  |  |  |--phoenix.js
|  |--templates
|  |  |--layout
|  |  |  |--application.html.eex
|  |  |--page
|  |  |  |--index.html.eex
|  |--views
|  |  |--error_view.ex
|  |  |--layout_view.ex
|  |  |--page_view.ex
|  |--web.ex

```

* routeに新規パスを追加

  `http://localhost:4000/hello`にアクセスすると`hello phoenix.`と表示するようにしてみる

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
    get "/hello", HelloController, :index ☆
  end

  # Other scopes may use custom stacks.
  # scope "/api", SamplePhoenix do
  #   pipe_through :api
  # end
end
```
`web/router.ex`の`scope "/"`ブロックに`get "/hello", HelloController, :index`を追加

* コントローラ追加

`web/controllers/hello_controller.ex`を以下内容で作成する

```elixir
defmodule SamplePhoenix.HelloController do
  use SamplePhoenix.Web, :controller

  plug :action

  def index(conn, _params) do
    render conn, "index.html"
  end
end
```

indexメソッドの引数
- coon
  リクエストに関するデータ
- _params
  リクエストパラメータ

indexメソッドの処理<br>
`render conn, "index.html"`でPhoenixフレームワークは`index.html.eex`を
`web/templates/コントローラ名`から探しす<br>
今回の場合は`web/templates/hello`

* Viewの作成

`web/views/hello_view.ex`を以下内容で作成する

```
defmodule SamplePhoenix.HelloView do
  use SamplePhoenix.Web, :view
end
```

* テンプレートの作成

phoenixフレームワークでのデフォルトのテンプレートエンジンは[eex](http://elixir-lang.org/docs/stable/eex/)<br>

`web/templates/hello/index.html.eex`を以下内容で作成する

```
<div class="jumbotron">
  <h2>Hello Phoenix!!</h2>
</div>
```

ここまできたら`mix phoenix.server`で確認してみる。
サーバを起動し`http://localhost:4000/hello`にアクセス

上記の画面が表示されていればOK
