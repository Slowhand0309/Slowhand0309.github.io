## PhoneGapを使用する 2 - PhoneGap + Grunt -

最近仕事でお世話になっているPhoneGapさん<br>

タイトルがPhoneGapを使用する **2** となってますが、<br>
実は[随分前](http://developabout0309.blogspot.jp/2014/10/phonegap-1.html)にPhoneGapを触っていた事があります。

その時から全然触ってなかったら、npmのバージョンが
```sh
$ npm -v
1.4.28
```
古い・・・古いすぎる!!<br>
早速バージョンアップ
```sh
$ npm update -g npm
$ npm -v
3.8.7
```
すごいバージョンの差 ^^;

そんなこんなで今回はPhoneGapをタスクツールのGruntと<br>
組み合わせてやってみたいと思います。

##### ちなみに・・・
****

`PhoneGap`と並んでよく出てくるワードで`Cordova`というのがあります<br>
両者の違いですが、公式のFAQでは

> PhoneGap is an open source distribution of Cordova. Think about Cordova’s relationship to PhoneGap like WebKit’s relationship to Safari or Chrome.

となっています。<br>
CordovaをWebkitに例えるならば、PhoneGapはWebKitから派生したSafariやChromeみたいなもので、PhoneGapはCordovaを内包してるようなイメージです。

実際、PhoneGapはCordovaの全ての機能が使えます。

##### 環境セットアップ
****

※ ここではPhoneGapのコマンドラインツールである`PhoneGap CLI `を使用します!!

↑のnpmのアップデートをやりましたが、最初からセットアップする場合、<br>
`Node.js`のインストールが必要です。

[Node.js](https://nodejs.org/en/)

次に最新の`PhoneGap CLI`をインストールします。
```sh
$ npm install -g phonegap@latest
```

##### プロジェクト作成から実行まで
****

次にプロジェクト作成〜実行まで一気にやりたいと思います。
```sh
# プロジェクトの作成
$ phonegap create <path> --id <id> --name <projectname>
# プラットフォームの追加(ios や androidなど)
$ phonegap cordova platform add <ios/android> --save
# ビルド
$ phonegap cordova build <ios/android>
# 実行
$ phonegap cordova run <ios/android>
```

このビルドと実行をGruntのタスク化にしてみたいと思います。<br>
タスク化するにあたり`grunt-exec`をインストールします。

```sh
$ npm install grunt-exec --save-dev
```
