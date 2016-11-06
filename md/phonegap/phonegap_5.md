## PhoneGapを使用する 5 - React Hot Reloading Templateを試す -

Phonegapの公式テンプレートで<br>
**[react-hot-loader](https://github.com/phonegap/phonegap-template-react-hot-loader)**があります。(※筆者のPhonegapのバージョン:6.3.2)

```sh
$ phonegap template list
・・・省略・・・
react-hot-loader   Starter PhoneGap project using React.js, Babel, Webpack and Hot Reloading.
```

PhonegaでReact.jsを使えて、環境はBabel、WebpackでHot Reloadingする<br>
という事なので早速試してみたいと思います。

#### プロジェクト作成
****

プロジェクトの作成は以下のコマンドでサクッと作成できます。
```sh
$ phonegap create [プロジェクト名] --template react-hot-loader
```

作成したプロジェクトの構成は次のようになっています。
```
.
├── config.js
├── config.xml
├── env.js
├── hooks
├── package.json
├── platforms
├── plugins
├── res
├── src
│   ├── components
│   │   ├── Hello.js
│   │   └── Message.js
│   ├── css
│   │   ├── hello.css
│   │   ├── index.css
│   │   └── message.css
│   └── index.js
├── webpack.config.js
└── www
    ├── icon.png
    └── index.html
```
ちゃんと`webpack.config.js`が用意されています。<br>
`.jsx`が見当たらないですが、Hello.js、Message.jsで実装されてます。

次に依存パッケージをインストールします。
```sh
$ npm install
```

これで準備完了です。

#### ブラウザで実行&確認
****

それでは早速実行してみたいと思います。<br>
まずはブラウザで確認。

```sh
$ npm start
```
起動完了後`http://localhost:8080`を見てみると

![gif1](http://slowhand0309.github.io/images/blog/phonegap/phonegap_5_1.gif)

↑のような画面が表示されており。ボタンを押すと「Hello World」と表示され<br>
しばらく経つと消えます。

次にHot Reloadが効いているか確認してみます。

![gif2](http://slowhand0309.github.io/images/blog/phonegap/phonegap_5_2.gif)

ちょっと分かりにくいかもしれませんが、ボタンの文字を修正し<br>
保存するとちゃんとブラウザ側のボタンに反映されています。

#### iOSシュミレータで確認
****

次はiOSのシュミレータで確認してみます。<br>
まずはiOS用のプラットフォームを追加。
```sh
$ phonegap cordova platform add ios
```

次にシュミレータで動作する用にビルドしてやる必要があります。<br>
ビルドせずに動かすと以下のエラーが発生します!!
```
Failed to load webpage with error: Could not connect to the server.
```

また、`Phonegap`を使用している場合、`package.json`を以下のように修正します。
```json
・・・省略・・・
"scripts": {
  "android": "phonegap cordova run android",
  "ios": "phonegap cordova run ios",
  "prepare": "node config && webpack && phonegap cordova prepare",
  "build": "node config && webpack && phonegap cordova build",
  "start": "node config && HOST=0.0.0.0 webpack-dev-server",
  "test": "echo \"No tests (yet!) -- submit a PR?\" && exit 0"
},
・・・省略・・・
```

あとは、
```sh
$ npm run build -- ios
$ npm run ios
```
で↓のようにシュミレータで動作できればOKです。

![gif3](http://slowhand0309.github.io/images/blog/phonegap/phonegap_5_3.gif)

#### まとめ
****

Hot Reloadもできて、Babel, Webpackも揃っているので、<br>
とても使いやすいのではないかと思います。<br>
今後PhonegapでReact.jsを使う必要がある場合はオススメです。
