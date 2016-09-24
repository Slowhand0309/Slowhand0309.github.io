## Reactを使う 4 - state -

今回は`state`を使ってみたいと思います。

> state

`props`との違いは、値の変更が可能で<br>
コンポーネント作成後でも値を設定できます。

サンプルとして今回はいいねボタンを作成してみたいと思います。<br>
最終的な完成版がこちら↓

![gif1](http://slowhand0309.github.io/images/blog/react/react_4.gif)

しょぼいというツッコミは無しで・・

##### コンポーネント作成
****

構成は今までと同じで以下の通りです。
```
.
├── index.html
├── package.json
├── src
│   ├── container.js
│   └── good_button.jsx ☆
└── webpack.config.js
```

今回新たに`good_button.jsx`を作成します。<br>
最終的な内容がこちら↓
```js
import React from 'react';
import ReactDOM from 'react-dom'

class GoodButton extends React.Component {
  constructor() {
    super();
    // [1]
    this.state = {
      goods: 0
    };
    this.handleClick = this.handleClick.bind(this); // [4]
  }

  handleClick() {
    this.setState({goods: this.state.goods + 1}); // [3]
  }

  render() {
    return (
      // [2]
      <button onClick={this.handleClick}>
        +{this.state.goods}
      </button>
    );
  }
}

ReactDOM.render(
  <GoodButton />,
  document.getElementById('container')
);
```

`GoodButton`クラスは今回**goods**という`state`の値を持っています。<br>

まずコンストラクタで`goods`を`0`に初期化しています。・・・[1]<br>

次にボタンが押されたイベントで`this.handleClick`を指定しています。・・・[2]

`handleClick`では現在の`goods`の値を+1しています。・・・[3]

とまあいたってシンプルです。<br>

ちなみに・・<br>
[4]の処理は何をしているかというと、`handleClick`内で`this`を使ってますが<br>
[4]の処理がないと`button`のthisになってしまいエラーになってしまいます。<br>

試しに[4]の処理をコメントアウトして実行するとコンソールに<br>
以下のエラーが表示されました。

img1

今回のはしょぼいですが、CSSをちゃんと使ってかっこいいボタンも作れると思います。
