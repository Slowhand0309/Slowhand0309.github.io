## Feedlyの未読記事をSlackへGASで通知するBotの作成 3

[前回](http://developabout0309.blogspot.jp/2016/07/feedlyslackgasbot-2.html)の続きです。

前回未読一覧を取得したので、今回は未読記事のタイトルとURLを<br>
SlackにPostするようにして完成させたいと思います。


##### ID毎の記事一覧取得
*****

前回、

例)
```json
{
  "unreadcounts": [
    {
      "count": 508,
      "id": "feed/http://www.autoblog.com/rss.xml",
      "updated": 1367539068016
    }
  ]
}
```
上のようなidを取得しましたが、そのidにぶら下がる記事一覧を取得します。<br>
試しに以下のurlを叩いてみてください。

```sh
$ curl -H 'Authorization: OAuth {トークン}' \
  https://cloud.feedly.com/v3/streams/contents?streamId={id}
```

実行すると以下のようなJSONが取得できたかと思います、

例)
```json
{
  "direction": "ltr",
  "title": "The Verge -  All Posts",
  "id": "feed/http => //www.theverge.com/rss/full.xml",
  "continuation": "gRtwnDeqCDpZ42bXE9Sp7dNhm4R6NsipqFVbXn2XpDA=_13fb9d6f274:2ac9c5:f5718180",
  "updated": 1367539068016,
  "items": [
    {
      "engagement": 15,
      "published": 1367539068016,
      "crawled": 1367539068016,
      "title": "NBC's reviled sci-fi drama 'Heroes' may get a second lease on life as Xbox Live exclusive", ★
      "author": "Nathan Ingraham",
      "id": "gRtwnDeqCDpZ42bXE9Sp7dNhm4R6NsipqFVbXn2XpDA=_13fb9d6f274:2ac9c5:f5718180",
      "content": {
        "direction": "ltr",
        "content": "..."
      },
      "updated": 1367539068016,
      "unread": true,
      "tags": [
        {
          "id": "user/c805fcbf-3acf-4302-a97e-d82f9d7c897f/tag/inspiration",
          "label": "inspiration"
        }
      ],
      "origin": {
        "title": "The Verge -  All Posts",
        "htmlUrl": "http://www.theverge.com/",
        "streamId": "feed/http://www.theverge.com/rss/full.xml"
      },
      "alternate": [
        {
          "href": "http://www.theverge.com/2013/4/17/4236096/nbc-heroes-may-get-a-second-lease-on-life-on-xbox-live", ★
          "type": "text/html"
        }
      ],
      "categories": [
        {
          "id": "user/c805fcbf-3acf-4302-a97e-d82f9d7c897f/category/tech",
          "label": "tech"
        }
      ]
    }
  ],
  ・・・
}
```

この中で、Slackに`title`と`href`をPostします(★が付いている所)<br>

GASでSlackにPostするまでを実装したものが↓になります。

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

  STREAM_URL: 'https://cloud.feedly.com/v3/streams/contents?streamId=',

  SLACK_URL: 'https://hooks.slack.com/services/XXXXXXXXXXXXXXXXXXXXXXXXXXXXX',

  initialize: function() {
  },

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

    this.stream(feeds);
  },

  /**
   * Collect unread streams.
   *
   * @param {Array} unreads
   */
   stream: function(unreads) {

     var streams = {};

     var auth = this.auth();
     for (var i = 0; i < unreads.length; i++) {
       var id = unreads[i].id;
       var response = this.get(this.STREAM_URL + id + '&count=3', auth);

       var obj = JSON.parse(response.getContentText("UTF-8"));
       var table = [];
       for (var j = 0; j < obj.items.length; j++) {
         var item = obj.items[j];
         var content = {
           'title': item.title,
           'href': item.alternate[0].href
         }
         table.push(content);
       }
       streams[id] = table;
     }

     this.postSlack(streams);
   },

  postSlack: function(streams) {

    for (key in streams) {
      var table = streams[key];
      for (var i = 0; i < table.length; i++) {
        var payload = {
          'text': table[i].title + '\n' + table[i].href,
          'channel': '#general',
          'username': 'XXXXXX',
          'icon_url': 'http://XXXXX/XXXXX.png'
        };
        var r = this.post(this.SLACK_URL, payload);
        Logger.log(r.getContentText("UTF-8"));
      }
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
  },

  /**
   * Post to url with payload.
   *
   * @param {String} url
   * @param {Object} payload
   */
  post: function(url, payload) {

    var options = {
      'method' : 'POST',
      'payload' : JSON.stringify(payload)
    };

    return UrlFetchApp.fetch(url, options);
  }
};
```

`SLACK_URL`にはWebHooksのURLを設定します。<br>
URLの取得方法は[こちら](http://developabout0309.blogspot.jp/2015/10/googleanalyticsslackgasbot-1.html)を参考にして下さい。

`channel`や`username`、`icon_url`は好きなように設定して下さい。

また、今回は記事を最大3件分だけ取ってくるようにしています。=> `&count=3`<br>
結構Feedly放置してると、とんでもない数のPostが飛んでくるので制限をかけましたw


ここまでできたら、Google Driveにアップロードし`exec`を実行してみて下さい。<br>
Slackに未読記事がPostされてたら成功です。<br>
トリガーを設定して毎日定期的にPostされるようにすればBotの完成です。


一応このプロジェクトは

[こちら](https://github.com/Slowhand0309/FeedlySlackBot)

に上げてます。テンプレートからトークンやらその他設定を`.env`に書いておけば<br>
`main.js`を作ってくれるようになってます。

興味があれる方は是非:star:
