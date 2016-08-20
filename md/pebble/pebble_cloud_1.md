## オリジナルのPebbleアプリを作成 1 - 環境設定 -

以前「[Pebble Time Round 購入!!](http://developabout0309.blogspot.jp/2016/08/pebble-time-round.html)」という記事を書きました。<br>
それからずっとPebbleをつけてますが、スマホを取り出さずに通知の確認や、<br>
音楽の再生などがとても便利で活用してます。

そんなPebbleですが、Pebble上で動くアプリが結構簡単に作れるらしいので<br>
早速試してみようと思います。


##### 作成方法
****

Pebbleのアプリを作るには[CloudPebble](https://cloudpebble.net/)でWeb上で作る方法と、<br>
SDKをダウンロードしてpythonで開発していく方法があります。

今回は前者の`CloudPebble`を使ってアプリを作ってみたいと思います。<br>
また、開発時にはiOS/Android端末とPCが同ネットワーク上にいる必要があります。
##### CloudPebble
****

まずは[CloudPebble](https://cloudpebble.net/)にアクセスし、アカウントを持ってない場合は作成し、<br>
ログインします。ログイン後の画面は以下のようになってます。<br>
※iOS/Android端末のアプリのログインアカウントと同一である必要があります。

img1

早速プロジェクトを作ります。右上の**CREATE**ボタンを押します。<br>
するとダイアログが表示されるので、作りたいプロジェクトの内容を設定します。

img2

今回は最初なので`HelloWorld`を表示する`sample1`プロジェクトを作成します。<br>
作成後`hello_world.c`ファイルがWebエディッタ上で編集できるようになってます。

img3

では早速動かしてみたいと思います。<br>
まずは端末側の設定ですが、Pebbleアプリを立ち上げて、<br>
`Apps`>メニュー`Settings`を選択し、`Developer Mode`をONにします。

img4

`Apps`>メニュー`Settings`>`Developer(off)`を選択します。

img5

`Enable Developer Connections`をONにします。

img6

次に`CloudPebble`に戻って、左側のメニューから`COMPILATION`を選択します。

img7

`RUN BUILD`をクリックしビルドを行い、上部のタブ`PHONE`を選択し、<br>
`INSTALL AND RUN`を実行します。

無事Pebble上で実行されていれば成功です。
