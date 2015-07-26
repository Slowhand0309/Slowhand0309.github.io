## Phoenixのインストール&実行

#### インストール
***
前回まではChef-soloを使って構築したが、Phoenixのインストールに関してはChef-soloを使うまでもなさそうなので手動でインストール

* Hexパッケージマネージャをインストール

```
$ mix local.hex

Are you sure you want to install archive https://s3.amazonaws.com/s3.hex.pm/installs/1.0.0/hex.ez? [Yn] Y
2015-07-25 16:06:19 URL:https://s3.amazonaws.com/s3.hex.pm/installs/1.0.0/hex.ez [269416/269416] -> "/home/vagrant/.mix/archives/hex.ez" [1]
* creating .mix/archives/hex.ez
```
* phoenixインストール

```
$ mix archive.install https://github.com/phoenixframework/phoenix/releases/download/v0.13.1/phoenix_new-0.13.1.ez

Are you sure you want to install archive https://github.com/phoenixframework/phoenix/releases/download/v0.13.1/phoenix_new-0.13.1.ez? [Yn] Y
* creating .mix/archives/phoenix_new-0.13.1.ez
```
#### サンプルプロジェクト作成
***

- `sample-phoenix`プロジェクト作成

```
$ mix phoenix.new sample_phoenix

* creating sample_phoenix/config/config.exs
* creating sample_phoenix/config/dev.exs
* creating sample_phoenix/config/prod.exs
* creating sample_phoenix/config/prod.secret.exs
* creating sample_phoenix/config/test.exs
* creating sample_phoenix/lib/sample_phoenix.ex
* creating sample_phoenix/lib/sample_phoenix/endpoint.ex
* creating sample_phoenix/priv/static/robots.txt
* creating sample_phoenix/test/controllers/page_controller_test.exs
* creating sample_phoenix/test/views/error_view_test.exs
* creating sample_phoenix/test/views/page_view_test.exs
* creating sample_phoenix/test/support/conn_case.ex
* creating sample_phoenix/test/support/channel_case.ex
* creating sample_phoenix/test/test_helper.exs
* creating sample_phoenix/web/controllers/page_controller.ex
* creating sample_phoenix/web/templates/layout/application.html.eex
* creating sample_phoenix/web/templates/page/index.html.eex
* creating sample_phoenix/web/views/error_view.ex
* creating sample_phoenix/web/views/layout_view.ex
* creating sample_phoenix/web/views/page_view.ex
* creating sample_phoenix/web/router.ex
* creating sample_phoenix/web/web.ex
* creating sample_phoenix/mix.exs
* creating sample_phoenix/README.md
* creating sample_phoenix/lib/sample_phoenix/repo.ex
* creating sample_phoenix/test/support/model_case.ex
* creating sample_phoenix/.gitignore
* creating sample_phoenix/brunch-config.js
* creating sample_phoenix/package.json
* creating sample_phoenix/web/static/css/app.scss
* creating sample_phoenix/web/static/js/app.js
* creating sample_phoenix/web/static/vendor/phoenix.js
* creating sample_phoenix/priv/static/images/phoenix.png

Fetch and install dependencies? [Yn] Y

Phoenix uses an optional assets build tool called brunch.io
that requires node.js and npm. Installation instructions for
node.js, which includes npm, can be found at http://nodejs.org.

After npm is installed, install your brunch dependencies by
running inside your app:

    $ npm install

If you don't want brunch.io, you can re-run this generator
with the --no-brunch option.

* running mix deps.get

We are all set! Run your Phoenix application:

    $ cd sample_phoenix
    $ mix phoenix.server

You can also run it inside IEx (Interactive Elixir) as:

    $ iex -S mix phoenix.server
```

* サーバー起動

```
$ cd sample_phoenix
$ mix phoenix.server
・・・
[info] Running SamplePhoenix.Endpoint with Cowboy on port 4000 (http)
[error] Could not start watcher "node", executable does not exist
```
なんかエラーがっ!!<br>
どうやらnode.jsが必要らしい>< ってプロジェクト作成時にコンソールに出てますね^^;
<br>
* epelリポジトリからインストール実施

```
$ rpm -ivh http://ftp.iij.ad.jp/pub/linux/fedora/epel/6/x86_64/epel-release-6-8.noarch.rpm
$ yum -y install nodejs npm --enablerepo=epel
```

* 再度サーバー起動

```
$ mix phoenix.server
[info] Running SamplePhoenix.Endpoint with Cowboy on port 4000 (http)

module.js:340
    throw err;
          ^
Error: Cannot find module '/home/vagrant/sample_phoenix/node_modules/brunch/bin/brunch'
    at Function.Module._resolveFilename (module.js:338:15)
    at Function.Module._load (module.js:280:25)
    at Function.Module.runMain (module.js:497:10)
    at startup (node.js:119:16)
    at node.js:929:3
```
またまたエラーがっorz<br>
`localhost:4000`にアクセスしてみるとこれじゃない感の画面が表示される

TODO 画像

これもプロジェクト作成時に出てる通りnode.jsがインストールされていないせいで
brunch.ioが使えていないから

* という事で再度プロジェクトの作り直し

```
$ mix phoenix.new sample_phoenix
・・・ 省略 ・・・
Fetch and install dependencies? [Yn] Y
* running npm install
* running mix deps.get


We are all set! Run your Phoenix application:

    $ cd sample_phoenix
    $ mix phoenix.server

You can also run it inside IEx (Interactive Elixir) as:

    $ iex -S mix phoenix.server

```

* 再度サーバー起動し`localhost:4000`にアクセスしてみる

TODO 画像

今度はちゃんと表示されました^^
