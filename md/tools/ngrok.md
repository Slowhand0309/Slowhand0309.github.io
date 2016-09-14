## ngrok - localhostを簡単に外部公開 -

開発している時に、ローカルの開発環境をテストサーバーに上げるのが面倒だったり、<br>
デモなどで少しの間だけ外から見れるようにしたい!!<br>
なんて事があるかと思います。

そんな時には**ngrok**が便利です。

> ngrokとは

[ngrok](https://ngrok.com)

localhostの環境を外から見れるようにセキュアなトンネルを作ってくれる<br>
便利なツールです。

#### インストール
***

[ダウンロードのページ](https://ngrok.com/download)に行って、環境にあったバイナリファイルを<br>
ダウンロードするだけです。

筆者の場合(MacOS)ですが、ダウンロードして、バイナリファイルをパスの通ってる<br>
`/usr/local/bin`配下に置きました。

ちゃんとインストールできたかは、
```sh
$ ngrok version
ngrok version 2.1.3
```
で確認できます。

#### 実行
***

試しに、簡単なRackアプリケーションをlocalhostで起動し、<br>
外部に公開してみます。

まずは普通にRackアプリケーション起動
```sh
$ bundle exec rackup
[2016-09-11 01:07:29] INFO  WEBrick 1.3.1
[2016-09-11 01:07:29] INFO  ruby 2.1.0 (2013-12-25) [x86_64-darwin14.0]
[2016-09-11 01:07:29] INFO  WEBrick::HTTPServer#start: pid=46061 port=9292
```
↑こんな感じで起動できました。この時`http://localhost:9292`で<br>
アクセスできます(ポート=9292)

そして実際`ngrok`を使って外部に公開してみます。

```sh
$ ngrok http 9292
ngrok by @inconshreveable        (Ctrl+C to quit)

Tunnel Status                 online
Version                       2.1.3
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://XXXXXX.ngrok.io -> localhost:9292
Forwarding                    https://XXXXXX.ngrok.io -> localhost:9292
```

エラーが起きなければ↑こんな感じで起動します。<br>
そして実際外部からアクセスする際は`http://XXXXXX.ngrok.io`または<br>
`https://XXXXXX.ngrok.io`でアクセスします。

実際にアクセスしてみると、Rackアプリケーションを立ち上げたコンソールに<br>
アクセスログが残っているかと思います。

#### 管理画面
***

また、`ngrok`起動中に`http://localhost:4040`にアクセスすると<br>
管理画面が表示されます。

img01

ステータスの表示や

img02

リクエストの詳細などが見れます。

img03

ん〜便利。
