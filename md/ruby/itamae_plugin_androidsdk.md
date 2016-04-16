## Gem公開 - Itamaeのplugin Android SDK -

ちょっと前に[記事](http://developabout0309.blogspot.jp/2016/01/itamae1.html)に上げた`Itamae`というツールのプラグインを作りました。


* [Rubygems](https://rubygems.org/gems/itamae-plugin-recipe-android_sdk)

* [Github](https://github.com/Slowhand0309/itamae-plugin-recipe-android_sdk)

> そもそもItamaeのプラグインって?

Itamaeには他のレシピを取り込めるようになっており、<br>
`include_recipe 'hogehoge'`のように書くと他のレシピを取り込む事が出来ます。

これによって様々なプラグインを取り込む事ができます。<br>
また、プラグインは`gem`として公開、インストールが可能です。

> 何を作った?

そのプラグインとして今回作成したのが<br>
`Android SDK`をインストールするプラグインです。

`Android SDK`自体は公開されているものをダウンロードしパスを設定すれば<br>
使用する事が出来ますが、plugin化した理由として

* aaptが32bitアプリケーションなので64bitの環境だと動かない

* sdkのアップデート(build-toolsやplatform等のインストール)を一気にやりたい

があった為です。

今回作成したプラグインでは上の二つを解決するべく、<br>
64bit環境では32bitの`aapt`が使えるように別途ライブラリをインストールし、<br>
アップデートしたいものを`node.json`で指定できるようにしています。


#### 使い方
***

まずインストールですが、`gem`として公開しているので、
```sh
$ gem install itamae-plugin-recipe-android_sdk
```
とするか、Gemfileに`gem 'itamae-plugin-recipe-android_sdk'`を追加し<br>
```sh
$ bundle install
```
とする事でインストールが可能です。

簡単なサンプルとして
```ruby
require 'itamae'

include_recipe 'android_sdk::install'
```
として実行すると、デフォルトで`/usr/local`にAndroid SDKがインストールされ<br>
`ANDROID_HOME`が環境変数として設定されます。<br>
Android SDKのバージョンとしては現時点で最新の`r24.4.1`がインストールされます。


また実行時にnode属性として以下が指定できます。
```json
{
  "sdk": {
    "version" : "r24.4",
    "install_path" : "/var/lib",
    "update_list" : [
      "build-tools-23.0.3",
      "android-22"
    ]
  }
}
```
上の例では

* SDKのバージョンが`r24.4.0`
* インストール先が`/var/lib`
* `build-tools-23.0.3`と`android-22`(platform)のインストール

を指定しています。

※ SDKのバージョンや、build-toos等のバージョンは[こちら](http://developer.android.com/intl/ja/tools/revisions/index.html)を参照して下さい

node属性を`node.json`として保存した場合の、実際に実行するコマンドは
```sh
$ itamae ssh --host [ホスト名] -u [ユーザー名] --node-json node.json ファイル名.rb
```
になります。

#### まとめ
***

色々説明しましたが、<br>
ちょっと気になった方や、AndroidSDKインストールする必要がある際などにこのプラグインを使って頂けたら幸いです。<br>

また、[Github](https://github.com/Slowhand0309/itamae-plugin-recipe-android_sdk)で公開しているので、何か問題点や不明点などがあれば`Issues`をあげて頂ければとても助かります。<br>

ついでに`Star`もつけて頂けるとモチベーションにつながるので^^;、<br>つけてやってもいいという方は宜しくお願いします。
