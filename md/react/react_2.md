## Reactを使う 2 - JSXとprop -

[前回](http://developabout0309.blogspot.jp/2016/09/react-1.html)に続いてReactをやって行こうと思います。

##### JSX
****

前回の構成ですが、
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
こんな感じでした。(なぜかREADME.mdが入っちゃってますが無視して下さい)

大まかな起動の流れとしては、

1. `webpack.config.js`で指定したパスのモジュールをbabelで変換

2. この時`hello.jsx`がjsにコンパイルされる

3. コンパイルされ出力された`bundle.js`をindex.htmlでロード

このような流れになります。

ここで、`JSX`というのが出てきましたが、何かというと・・・<br>
XML-likeにタグを書いてjsにコンパイルして使うものです。

詳しくは[こちら](http://facebook.github.io/jsx/)

##### prop
****

次はただ表示させるだけのサンプルではなく、動くものを作ってみたいと思います。

と言う訳で画面に現在時刻を表示させてみたいと思います。<br>
前回の`hello.jsx`を以下のように修正します。

```js
import React from 'react';
import ReactDOM from 'react-dom'

class Hello extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello</h1>
        <p>
          Now : {this.props.date.toTimeString()}
        </p>
      </div>
    );
  }
}

setInterval(function() {
  ReactDOM.render(
    <Hello date={new Date()} />,
    document.getElementById('container')
  );
}, 1000);
```

実行して↓のように現在時刻をリアルタイムに表示してくれてればOKです。

![gif](http://slowhand0309.github.io/images/blog/react/react_2.gif)

この時
```
ERROR in ./src/hello.jsx
Module build failed: SyntaxError:
/Users/XXX/src/hello.jsx:
Adjacent JSX elements must be wrapped in an enclosing tag (8:6)
```
このようなエラーが出る場合はタグがちゃんと囲まれてない場合があります。<br>
※ どうもJSXでは一つのタグで囲まれたものしかreturnできないようです

ここで出てきた`props`ですがこれは親コンポーネントなどから渡されたデータを<br>
子コンポーネントのプロパティとして扱う事ができ、アクセスする際は`props`を使用します。

上のサンプルでは`setInterval`で1秒おきに描画してますが、その際に<br>
`date={new Date()}`でdateに現在時刻を設定し、子コンポーネントのHelloで<br>
`{this.props.date.toTimeString()}`にてプロパティから`date`にアクセスしています。
