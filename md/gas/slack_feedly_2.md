## Feedlyの未読記事をSlackへGASで通知するBotの作成 2

[前回](http://developabout0309.blogspot.jp/2016/07/feedlyslackgasbot-1.html)の続きです。

前回はプロフィール情報をログ出力する所までやりました。<br>
今回はまずは未読の件数を取得してみたいと思います。

##### 未読件数一覧
*****

早速試しに以下コマンドを叩いてみたください。
```sh
$ curl -H 'Authorization: OAuth {トークン}' https://cloud.feedly.com/v3/markers/counts
```

ずらずらっと件数とidがセットで返ってくると思います。<br>
例)
```json
{
  "unreadcounts": [
    {
      "count": 605,
      "id": "user/c805fcbf-3acf-4302-a97e-d82f9d7c897f/category/global.all",
      "updated": 1367539068016
    },
    {
      "count": 601,
      "id": "user/c805fcbf-3acf-4302-a97e-d82f9d7c897f/category/design",
      "updated": 1367539068016
    },
    {
      "count": 508,
      "id": "feed/http://www.autoblog.com/rss.xml",
      "updated": 1367539068016
    }
  ],
  "updated": 1367539057683
}
```

idが**user/**から始まっているものは<br>
`user/・・・/category/global.all`が全未読記事数<br>
`user/・・・/category/design`がカテゴリー毎の未読記事数<br>
となっています。

またidが**feed/**から始まっているものは<br>
それぞれ登録したブログ毎の未読記事数となります。


##### feed毎の未読記事件数を取得
*****

前回プロフィール表示した時の`src/main.js`を修正します。

```js
/**
 * Entry function.
 */
function exec() {
  FeedlySlackBot.unread();
}

/**
 * FeedlySlackBot object.
 */
var FeedlySlackBot = {

  PROFILE_URL: 'https://cloud.feedly.com/v3/profile',
  UNREAD_COUNT_URL: 'https://cloud.feedly.com/v3/markers/counts',

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
   * Get unread feeds.
   */
  unread: function() {

    var auth = this.auth();
    var response = this.get(this.UNREAD_COUNT_URL, auth);

    var feeds = [];
    // Parse json.
    var obj = JSON.parse(response.getContentText("UTF-8"));
    var unreadcounts = obj.unreadcounts;
    for (var i = 0; i < unreadcounts.length; i++) {
      var unread = obj.unreadcounts[i];
      if (unread.count > 0 && unread.id.indexOf('feed/') > -1) {
        Logger.log('unread => id : ' + unread.id + ', count : ' + unread.count);
        feeds.push(unread);
      }
    }

    if (feeds.length == 0) {
      Logger.log('Unread feed not found.');
      return;
    }
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

追加した`unread`メソッドで未読件数をGETするリクエストを投げて、<br>
結果を`feeds`の配列に追加していってます。<br>
その際に件数が1件以上、idが`feed/・・`のみでフィルタリングしてます。

GoogleDriveにアップロードして`exec`を実行するとログに<br>
ブログ毎の未読件数一覧が表示されているかと思います。

##### アクセストークンが有効期限切れの場合
*****

developer用のアクセストークンは30日で有効期限が切れます。<br>
アクセストークンが有効期限切れの場合、以下のようなメッセージが帰ってきます。

```
{"errorCode":401, "errorId":"ap2-sv2.XXXXXXXXXX.XXXXXXX", "errorMessage":"token expired: XXXXXXXXXXX (-XXXXXX)"}%
```

`401`が帰ってきた場合は再度アクセストークンを作り直す必要があります。<br>
ここら辺を継続的にできるようになればいいのですが・・

次回は未読記事のタイトルとURLをSlackにポストするまでをやってみたいと思います。
