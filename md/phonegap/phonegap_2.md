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

※ と思ったらphonegapコマンドで以下のようなエラーが・・・
```sh
module.js:340
    throw err;
          ^
Error: Cannot find module 'phonegap-build-api'
    at Function.Module._resolveFilename (module.js:338:15)
    at Function.Module._load (module.js:280:25)
    at Module.require (module.js:364:17)
    at require (module.js:380:17)
    at Object.<anonymous> (/usr/local/lib/node_modules/phonegap/node_modules/phonegap-build/lib/phonegap-build/login.js:6:14)
    at Module._compile (module.js:456:26)
    at Object.Module._extensions..js (module.js:474:10)
    at Module.load (module.js:356:32)
    at Function.Module._load (module.js:312:12)
    at Module.require (module.js:364:17)
```

やっぱりアップデートが極端だったんでしょうか・・<br>
色々試してもダメだったので、phonegapを再インストールしてみ他ところエラーがなくなりました。
```sh
$ sudo npm uninstall -g phonegap
$ npm install -g phonegap
```

エラーがなくなった所でビルドと実行をGruntのタスク化にしてみたいと思います。<br>
タスク化するにあたり`grunt`と`grunt-exec`をインストールします。

```sh
$ npm install -g grunt-cli
$ npm install grunt --save-dev
$ npm install grunt-exec --save-dev
```

プロジェクトのビルドと実行をタスク化してみたいと思います。

```js
module.exports = function(grunt) {
  grunt.loadNpmTasks('grunt-exec');

  grunt.initConfig({
   exec: {
      build: {
         command: 'phonegap cordova build'
      },
      run: {
         command: 'phonegap cordova run'
      }
   }
  });

  grunt.registerTask('default', ['exec:build', 'exec:run']);
};
```

これで
```sh
$ grunt
```
とするとビルドと実行が可能になりました^^。
