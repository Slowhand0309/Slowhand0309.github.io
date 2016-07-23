## Feedlyの未読記事をSlackへGASで通知するBotの作成 1

現在RSSリーダーは主に**[Feedly](https://feedly.com/
)**を使ってるんですが、<br>
未読記事を定期的にSlackにポストしてくれたら便利かな〜と思って<br>
作ってみることにしました。

※ もしかしたら既にBotがある?かもしれませんが・・

##### 完成イメージ
*****

以前**[GoogleAnalyticsの内容をSlackへGASで通知するBotの作成](http://developabout0309.blogspot.jp/2015/10/googleanalyticsslackgasbot-1.html)**という記事を書きましたが、<br>
今回も先に完成イメージを書きます。


##### 事前準備
*****

まずは**[ここ](https://feedly.com/v3/auth/dev)**からログインし、FeedlyのDeveloper用のトークンをゲットします。

img1

※ Feedlyのアカウントを持ってない方はこの機会にどうぞ<br>
  (:warning:Feedlyのまわし者ではありません)

ログインすると登録しているメールアドレスに認証用のメールが送られます。

img2

送られたメールのリンクをクリックするとアクセス用のトークンがもらえます。

img3

FeedlyのAPIを呼ぶ際に必要なトークンとなります。

試しに自分のプロフィール情報を取ってみます。
```sh
$ curl -H 'Authorization: OAuth {トークン}' https://cloud.feedly.com/v3/profile
```

`{トークン}`部分に上で取得したトークンを貼り付けます。<br>
実行するとずらずらっとプロフィール情報が取得できたかと思います。

##### 開発環境
*****

次にローカルでの開発環境ですが、<br>
こちらも以前の[記事](http://developabout0309.blogspot.jp/2016/06/gasclinode-google-apps-script.html)で紹介した**node-google-apps-script**を使用します。<br>
初期設定などは上の記事を参照して下さい。

※事前に`node.js`のインストールが必要です:warning:。

まずはパッケージ情報や依存するパッケージなどを記述する`package.json`を作成します。

```sh
$ npm init
name: (....) feedly_slack_bot⏎ # 名前はfeedly_slack_bot
version: (1.0.0) ⏎
description: ⏎
entry point: (index.js) main.js ⏎ # 今回はmain.jsがメインファイル
・・・ 残りは好きなように設定
Is this ok? (yes) yes ⏎
```

以下のような`package.json`が作成されているかと思います。
```json
{
  "name": "feedly_slack_bot",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Slowhand0309",
  "license": "MIT"
}
```

ここで肝心の`node-google-apps-script`をインストールします。
```sh
$ npm install node-google-apps-script --save-dev
```

これでGASへのアップロードがCLIで行えるようになります。


##### プロフィール表示
*****

試しにプロフィールを表示するまでをやってみます。<br>
`gapps init`を行い、ローカルに`gapps.config.json`と`src/*.js`<br>
をダウンロードしてきます。

`src/main.js`とし、↓に編集します。
```js
/**
 * Entry function.
 */
function exec() {
  FeedlySlackBot.profile();
}

/**
 * FeedlySlackBot object.
 */
var FeedlySlackBot = {

  PROFILE_URL: 'https://cloud.feedly.com/v3/profile',

  /**
   * Get auth string.
   *
   * @return {String}
   */
  auth: function() {
    return 'OAuth {トークン}';
  },

  /**
   * Log out profile info.
   */
  profile: function() {

    var auth = this.auth();
    var response = this.get(this.PROFILE_URL, auth);
    Logger.log(response.getContentText("UTF-8"));
  },

  /**
   * Get response with auth.
   *
   * @param {String} url
   * @param {String} auth
   */
  get: function(url, auth) {
    var headers = {'Authorization' : auth};
    var options = {
      'method' : 'get',
      'contentType' : 'application/json;charset=utf-8',
      'headers' : headers
    };

    return UrlFetchApp.fetch(url, options);
  }
};
```

{トークン}は上で取得したトークンを設定して下さい。

アップロードし、
```sh
$ gapps upload
```

Google Drive上で`exec`を指定し実行するとログにプロフィール情報が<br>
出力されているかと思います。

img4

今回はここまで
