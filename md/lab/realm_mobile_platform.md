## 新しい技術を学んでみる 7 - Realm Mobile Platform -

ついこの間発表された`Realm Mobile Platform`を試してみたいと思います。

> Realmとは?

SQLiteやCore Dataの代替となるモバイルのデータベース<br>
Android、iOS両方で使える。

> Realm Mobile Platformとは?

データベースに加え、リアルタイムの同期、コンフリクトの解決、<br>
イベントハンドリングといったサーバサイドの機能をシームレスに統合したもの。<br>
現在はJava、Objective-C、Swiftのみ対応。

#### 環境準備
***

開発環境としてはMacOSで進めていきます。<br>
MacOS以外ではLinuxでも使えるようです。

まずは[ここ](https://realm.io/docs/realm-mobile-platform/get-started/)から一式をダウンロードします。

img1

ダウンロードしたフォルダの中に`start-object-server.command`があるので、<br>
ダブルクリックして`Realm Object Server`を立ち上げます。

Server起動するとブラウザが立ち上がりAdminユーザー作成画面が表示されます。

img2

e-mailとpasswordを入力しAdminユーザーを作成します。<br>
セットアップが完了すると再度e-mailとpasswordを求められるので入力しLoginします。

img3

ログイン後ダッシュボード画面が表示されればセットアップ完了です。

img4

#### デモを試してみる
***

早速何か動かしてみたいので、[チュートリアル](https://realm.io/docs/realm-mobile-platform/get-started/#running-realmtasks)にある`RealmTasks`を<br>
動かしてみたいと思います。

先ほどダウンロードした`realm-mobile-platform`フォルダ内に<br>
`RealmTasks.app`があるので起動してみます。

img5

起動するとe-mailとpasswordを求められるので、<br>
適当なユーザーを作成してログインします。(※ローカル内だけでのアカウントなので適当で)

img6

次に「+」を押して新規タスクを作成してみます。

img7

タスクは左にドラッグで削除、右にドラッグでタスク完了の操作ができます。

#### Realm Browser
***

デモアプリを起動している状態で、サーバーと同期が取れているか確認してみます。<br>
`realm-mobile-platform`フォルダ内にある`Realm Browser.app`を起動します。<br>
起動したら`Connect to Object Server`を押します。

img8

`Server URL`と`Admin Access Token`を入力する画面で<br>
デフォルトの場合

* Server URL

  realm://localhost:9080

* Admin Access Token

  `start-object-server.command`をダブルクリックした時コンソールに出力されているAdmin Access Token

を入力します。

img9

`Realm Object Server`に接続された`Realm Browser`では<br>
デモアプリと同期しているのが分かります。

![gif](http://slowhand0309.github.io/images/blog/lab/realm.gif)

#### 使ってみて
***

今回はデモを動かしてみただけですが、色んな用途で使えそうです。<br>
次は実際にアプリに組み込んで、組み込み易さを検証したいと思います。<br>
また、今回はローカル内で完結していたのでレスポンスが早いのは当たり前ですが<br>
実際のサーバー上とのやりとりでどんな感じが試してみたいと思います。
