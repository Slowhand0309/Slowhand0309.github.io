## GoogleAnalyticsの内容をSlackへGASで通知するBotの作成

GoogleAnalyticsでアクセス数を見る場合、毎回GoogleAnalyticsへログインし<br />
見たい情報を見てたんですが、結構面倒くさいのでSlackへ通知してくれるBotを<br />
Google Apps Scriptで作成しました。

その内容を長くなりそうなので、2回に分けて投稿したいと思います

<br />

> **[Slack](https://slack.com)**

&nbsp;&nbsp;チャットを中心としたチームコミュニケーションツール<br />
&nbsp;&nbsp;色々なサービスと連携できる

<br />
> **[GoogleAnalytics](http://www.google.com/analytics/)**

&nbsp;&nbsp;Googleが提供する高機能な無料アクセス解析ツール

<br />

> Google Apps Script

&nbsp;&nbsp;[以前の投稿](http://developabout0309.blogspot.jp/2015/01/gas-1.html)を参考

<br />

#### 完成イメージ
*****
まず作成するBotがどんなものになるかキャプチャを載せてます↓

No.001

指定したタイミングでSlackにアクセス状況を通知しています

#### SlackでWebHooks URLの取得
***

まずは通知したいSlackのTeamのWebHooksのトークンを取得する<br />

* 「Configure Integrations」> 「Incoming WebHooks」を選択

No.002

* Channelを選択し「Add Incomming WebHooks Integration」を選択

No.003

※後でGASからPOSTするChannelは指定するので、ここでは適当なChannelを選択

* 表示されたWebHooks URLを保存しておく

No.004


#### Google Analytics APIの有効化
***

Google Apps Script上でGoogle Analytics APIを使う為に有効化を行う

* スプレットシートを作成し、「ツール」>「スクリプトエディッタ」を開く

今回は「AnalyticsBot」の名前でスプレットシートとプロジェクトを作成
No.005

* 「リソース」>「Googleの拡張サービス」を選択

No.006

* 「Google Analytics API」を有効にする

No.007

* 「Google Developers Console」でも「Analytics API」を有効にする

[Google Developers Console](https://console.developers.google.com/)にアクセスし「APIと認証」>「API」を選択

No.008

「Analytics API」を選択

No.009

「APIを有効にする」

No.010

※これを有効にしてないと以下エラーが発生する

No.011

次回はGoogle Apps Scriptをゴリゴリ組んでいきます
