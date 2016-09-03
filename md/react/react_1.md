## Reactを使う

今更感もありますが、最近仕事でフロントエンド周りをやっていた為、<br>
色々勉強していこうと今回はReactに手を出してみます。


> Reactとは?

Facebookが開発しているJavascriptのライブラリで<br>
UIをコンポーネントベースで構築していきます。

https://facebook.github.io/react/

##### 開発環境構築
****

Reactが登場して、いろいろ周りの環境などが落ち着いて来たので<br>
以下の組み合わせで進めて行こうかと思います。

* **Babel**
* **webpack**
* **ESLint**
* **Mocha**

それぞれどんなものかというと・・・

> Babel

https://babeljs.io

2015年に`ECMAScript(ES6)`が登場しました、何かというと・・・<br>
Javascriptの標準化されたものが`ECMAScript`でEcmaインターナショナル<br>
の元で標準化が行われています。

そして、2009年に発表されたものが`ES5`で、現在のモダンなブラウザは<br>
ほとんど実装されています。ただ`ES6`はまだ対応中です。。<br>
そこで**Babel**を使うと`ES6`や次期のバージョンのソースコードから<br>
旧バージョンの`ES5`などに変換します。

> webpack

https://webpack.github.io

Webアプリケーションに必要なリソースや依存関係などを解決する<br>
ビルドツールです。

今回はwebpackでbabelなどの必要なものを取り入れてビルドします。

> ESLint

http://eslint.org

Javascriptの静的検証ツールです。<br>
検証ルールを設定できES6の構文やjsxもチェックできます。

> Mocha

https://mochajs.org

Unitテスト用フレームワークです。

##### 簡単なサンプル作成
****

今回はただ**Hello**と表示させるだけの簡単なサンプルを作成するので<br>
`Mocha`と`ESLint`は使いませんが、とりあえずインストールだけしています。

早速もろもろインストールします。
```sh
$ npm init
$ npm install -g webpack
$ npm install -g webpack-dev-server
$ npm install -g mocha
$ npm install --save react
$ npm install --save react-dom
$ npm install --save-dev babel-core
$ npm install --save-dev babel-loader
$ npm install --save-dev babel-preset-es2015
$ npm install --save-dev babel-preset-react
$ npm install --save-dev eslint
```

`package.json`に関しては今回はこんな感じで作成しました。
```js
{
  "name": "Sample",
  "version": "1.0.0",
  "description": "React sample.",
  "main": "container.js",
  "scripts": {
    "lint": "eslint src"
  },
  "author": "XXXXXXXX",
  "license": "MIT"
}
```
`scripts`にて`eslint`を起動できるようにしています。<br>
実際に動作させる場合は
```sh
$ npm run lint
```
で実行できますが、今回は使いません。


次に以下構成でファイルを作成していきます。
```
.
├── README.md
├── index.html
├── package.json
├── src
│   ├── container.js
│   └── hello.jsx
└── webpack.config.js
```

* webpack.config.js

```js
var path = require('path');

module.exports = {
  entry: './src/container.js',
  output: {
    path: __dirname,
    filename: 'bundle.js'
  },
  module: {
    loaders: [
      {
        test: /.jsx?$/,
        loader: 'babel-loader',
        exclude: /(node_modules|bower_components)/,
        query: {
          presets: ['es2015', 'react']
        }
      }
    ]
  },
};
```
上記設定では`babel`を使用して、`container.js`と`hello.jsx`から<br>
`bundle.js`を作成しています。<br>
最終的に`index.html`は`bundle.js`を読み込むようにしています。

* container.js

```js
import './hello.jsx';
```

* hello.jsx

```js
import React from 'react';
import ReactDOM from 'react-dom'

class Hello extends React.Component {
  render() {
    return <h1>Hello</h1>
  }
}

ReactDOM.render(<Hello/>, document.getElementById('container'));
```

* index.html

```html
<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>React Sample</title>
</head>
<body>
  <div id="container"></div>
</body>
<script type="text/javascript" src="bundle.js"></script>
</html>
```

それでは早速実行して確認してみたいと思います。<br>
↓の`webpack-dev-server`コマンドで色々よろしくやってくれます。
```sh
$ webpack-dev-server --progress --colors
```
起動後、

http://localhost:8080/webpack-dev-server/index.html

を確認して↓このように**Hello**と表示されとけば成功です。

img1
