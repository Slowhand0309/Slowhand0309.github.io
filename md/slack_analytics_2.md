## GoogleAnalyticsの内容をSlackへGASで通知するBotの作成2

前回に続いてBotを作成していきます
<br />

#### Google AnalyticsのProfileIDを取得
******

GoogleAnalyticsの指定したビューのProfileIDを取得する<br />
GoogleAnalyticsの構成については[公式ページ](https://support.google.com/analytics/answer/1009618?hl=ja&ref_topic=3544906&vid=1-635801747307115502-579477226)を参照

流れとしては、GASでProfileIDをログ出力するスクリプトを作成し<br />
スクリプトを実行しProfileIDを確認する

スクリプトは[公式ページ](https://developers.google.com/apps-script/advanced/analytics)のものを使用

以下のスクリプトをコピーする
```javascript
function listAccounts() {
  var accounts = Analytics.Management.Accounts.list();
  if (accounts.items && accounts.items.length) {
    for (var i = 0; i < accounts.items.length; i++) {
      var account = accounts.items[i];
      Logger.log('Account: name "%s", id "%s".', account.name, account.id);

      // List web properties in the account.
      listWebProperties(account.id);
    }
  } else {
    Logger.log('No accounts found.');
  }
}

function listWebProperties(accountId) {
  var webProperties = Analytics.Management.Webproperties.list(accountId);
  if (webProperties.items && webProperties.items.length) {
    for (var i = 0; i < webProperties.items.length; i++) {
      var webProperty = webProperties.items[i];
      Logger.log('\tWeb Property: name "%s", id "%s".', webProperty.name,
          webProperty.id);

      // List profiles in the web property.
      listProfiles(accountId, webProperty.id);
      }
  } else {
    Logger.log('\tNo web properties found.');
  }
}

function listProfiles(accountId, webPropertyId) {
  var profiles = Analytics.Management.Profiles.list(accountId,
      webPropertyId);
  if (profiles.items && profiles.items.length) {
    for (var i = 0; i < profiles.items.length; i++) {
      var profile = profiles.items[i];
      Logger.log('\t\tProfile: name "%s", id "%s".', profile.name,
          profile.id);
    }
  } else {
    Logger.log('\t\tNo web properties found.');
  }
}
```

「listAccounts」を選択し実行する

No.001

初回実行時は認証を求められます

No.002

実行が完了したらスクリプトエディッタ上で「表示」>「ログ」を選択し<br />
ProfileIDを確認する

No.003

一番下のProfileのIDを確認する<br />
※ビューを複数設定している場合は対象のProfile IDを確認する


#### Slackに通知するスクリプトの作成
******

GoogleAnalyticsから解析データを取得する
`Analytics.Data.Ga.get`メソッドを使用する

このメソッドはRESTfulエンドポイントとしてクエリすることも可能<br />
例)
```
https://www.googleapis.com/analytics/v3/data/ga
  ?ids=ga:12345
  &start-date=2008-10-01
  &end-date=2008-10-31
  &metrics=ga:sessions,ga:bounces
```

クエリパラメータ

* `ids`・・・ga:XXXX形式、XXXXにはProfile IDを設定する
* `start-date`・・・取得する解析データの開始日
* `end-date`・・・取得する解析データの終了日
* `metrics`・・・指標のリスト

上のパラメータは必須、他はオプションとして指定可能<br />
詳しくは[公式ページ](https://developers.google.com/analytics/devguides/reporting/core/v3/reference)を参照

↓スクリプト

```javascript
/**
 * メイン実行関数
 */
function reportAnalytics() {
  var profileId = "XXXXXXXXX"; // 確認したProfileID

  reportSummary(profileId);
}

/**
 * 現時点の解析結果をSlackへポスト
 *
 * @param profileId プロファイルID
 */
function reportSummary(profileId) {

  // ①
  var today = new Date();
  var toDate = Utilities.formatDate(today, Session.getTimeZone(), 'yyyy-MM-dd');  
  var tableId = "ga:" + profileId;
  var metric = "ga:users,ga:sessions,ga:pageviews";
  var options = {
    "max-results" : 25
  }

  // ②
  var text = "";
  var report = Analytics.Data.Ga.get(tableId, toDate, toDate, metric, options);
  if (report.rows) {
    var rows = report.getRows();
    text = "現在のブログ状況をお知らせします\n";
    // index:0 ユーザー数
    text += "ユーザー数 : " + rows[0][0] + "\n";
    Logger.log(rows[0][0]);
    // index:1 セッション数
    text += "セッション数 : " + rows[0][1] + "\n";
    Logger.log(rows[0][1]);
    // index:2 ページビュー数
    text += "ページビュー数 : " + rows[0][2] + "\n";
    Logger.log(rows[0][2]);
  }

  // ③
  if (text != "") {
     var payload = {
     "text" : "ピンポンパンポーン:musical_note:\n" + text + "\n",
     "channel" : "#general",
     "username" : "USERNAME",
     "icon_url" : "http://XXXXXXXXXXXXXXXXXXXXX"
     }
     postSlack(payload);
   }
}

/**
 * Slackポスト関数
 *
 * @param payload ポスト詳細
 */
function postSlack(payload) {

  var options = {
    "method" : "POST",
    "payload" : JSON.stringify(payload)
  }

  var url = "https://XXXXXXXX"; // SlackのWebHooks URL
  var response = UrlFetchApp.fetch(url, options);
  var content = response.getContentText("UTF-8");
}
```

大まかな流れとしては、<br />
① 解析データの取得パラメータ作成<br />
② 解析データを取得しSlackへ通知する文章を作成<br />
③ Slackへ通知<br />
になります

③で投稿するチャンネルを指定できます

#### 定期的に投稿するように設定
******

最後にトリガーを設定し定期的に実行するように設定してやります。<br />
スクリプトエディッタを開き「リソース」>「現在のプロジェクトのトリガー」を選択し<br />
実行を「reportAnalytics」に、イベントを「時間主導型」「時タイマー」「※好きな時間」<br />
を設定する

No.004

あとは指定した時間に投稿されとけばOK
