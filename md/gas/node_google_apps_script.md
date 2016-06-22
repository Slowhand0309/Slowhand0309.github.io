## GASのローカル開発用CLIツール(node-google-apps-script)を試す

[以前](http://developabout0309.blogspot.jp/2015/10/googleanalyticsslackgasbot-1.html)GoogleAnalyticsの内容をSlackへGASで通知するBotを作成しましたが、<br>
全てWeb上のスクリプトエディッタで作成していましたが<br>
今回試す**node-google-apps-script**を使う事で**ローカルで開発できる**:star:との事。

という事で早速使ってみたいと思います。

<br>

#### インストール
***

まずはインストール。※ npmはインストール済みの前提です。
```sh
$ npm install -g node-google-apps-script
```
<br>
正常にインストールされると`gapps`コマンドが使えるようになります。
```sh
$ gapps --version
1.1.5
```

<br>

#### 認証トークンの取得
***

[Google Developer Console](https://console.developers.google.com/)で「DriveAPI」> 「有効にする」を選択し<br>
DriveAPIを有効にします。

img1

「認証情報」> 「認証情報を作成」を選択。

img2

「OAuthクライアントID」を選択します。

img3

「その他」にチェックし作成します。

img4

作成したら認証情報のjsonファイルをダウンロードします。

img5

<br>

#### 初期設定
***

ダウンロードしたjsonファイルを指定して初期設定を行います。
```sh
$ gapps auth {jsonファイルのパス}
Please visit the following url in your browser (you'll only have to do this once): https://XXXXXX......
```
上のURLを開いて下さいとメッセージが出るのでブラウザで開いて設定完了です。<br>
成功すると以下メッセージが表示されます。

img6

#### 新規作成 & アップロード
***

GoogleDrive上で新規に`Google Apps Script`を作成します。<br>

img7

作成したファイルを開き「ファイル」> 「プロジェクトのプロパティ」を選択しプロジェクトキーをコピーします。

img8

以下コマンドでローカルに環境を構築します。

```sh
$ gapps init {コピーしたプロジェクトキー}
```

構築されると`gapps.config.json`に設定情報が、`src/**.js`が<br>
ソースファイルとなっています。

<br>

アップロードの際は以下コマンドを実行します。
```sh
$ gapps upload
Pushing back up to Google Drive...
The latest files were successfully uploaded to your Apps Script project.
```

:bangbang:もしアップロードの際に以下エラーが出た場合
```sh
Pushing back up to Google Drive...

/usr/local/lib/node_modules/node-google-apps-script/lib/util.js:18
        file = path.parse(filename);
                    ^
TypeError: Object #<Object> has no method 'parse'
    at /usr/local/lib/node_modules/node-google-apps-script/lib/util.js:18:21
    at /usr/local/lib/node_modules/node-google-apps-script/node_modules/node-dir/lib/readfiles.js:110:34
    at fs.js:271:14
    at Object.oncomplete (fs.js:107:15)
```

Node.jsのバージョンが古い可能性があります。<br>
Node.jsを最新いアップデートして再度実行すると上手くいくかと思います。
