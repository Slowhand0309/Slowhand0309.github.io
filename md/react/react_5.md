## Reactを使う 5 - ログイン画面 -

今回は簡単なログイン画面を作ってみたいと思います。

の前に・・余談ですがエディッタに`atom`を使っている場合、<br>
デフォルトだと`.jsx`ファイルを開いてもシンタックスハイライトされていません。

img1

そこで[react](https://atom.io/packages/react)パッケージをインストールすると`.jsx`ファイルが<br>
シンタックスハイライトされて表示されるようになります。

img2

本当にただの余談でしたw

##### コンポーネント作成
****

構成は今まで通りで今回は`login.jsx`を新たに作成します。<br>
まずは画面だけを作成します。`login.jsx`を次のように実装します。

```js
import React from 'react';
import ReactDOM from 'react-dom'

class Login extends React.Component {

  constructor() {
    super();
    this.state = {
      msg: 'none'
    };
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleSubmit(e) {
    console.log('handleSubmit');
  }

  render() {
    return (
      <div className="login">
        <h1>Login</h1>
        <form className="loing form" onSubmit={this.handleSubmit}>
          <input type="text" placeholder="Name" />
          <input type="text" placeholder="Password" />
          <input type="submit" value="Submit" />
        </form>
        <p>{this.state.msg}</p>
      </div>
    );
  }
}

ReactDOM.render(
  <Login />,
  document.getElementById('container')
);
```

↑で`class`ではなく`className`となっていますが`class`と同じ働きをします。<br>
また、↑では`Submit`ボタンを押してもコンソールにログが出るだけになってます。

実行結果

img3

##### イベント処理
****

次にSubmitボタンを押したら入力された名前とパスワードを<br>
画面に表示させてみたいと思います。

まずは`input`に`ref`を追加します。

* refとは?
> `input`などの要素にアクセスできるようにするもの<br>
要素へアクセスするには`ReactDOM.findDOMNode`を使う


という事で早速`ref`を追加してみます。

```js
<form className="loing form" onSubmit={this.handleSubmit}>
  <input type="text" placeholder="Name" ref={(ref) => this.inputName = ref} />
  <input type="text" placeholder="Password" ref={(ref) => this.inputPass = ref} />
  <input type="submit" value="Submit" />
</form>
```
コールバックとして受け取った`ref`を`this.inputName`や`this.inputPass`に設定しています。

次に`handleSubmit`を次のように修正します。

```js
handleSubmit(e) {
  e.preventDefault(); // デバッグ用

  let name = ReactDOM.findDOMNode(this.inputName).value.trim();
  let pass = ReactDOM.findDOMNode(this.inputPass).value.trim();
  let message = `name[${name}] pass[${pass}]`;

  this.setState({msg: message});
}
```
本来なら`Submit`ボタンが押されたらサーバー側で処理してログイン後の画面に<br>
遷移したりするのですが、今回はデモの為イベントを`preventDefault`によって<br>止めています。<br>

`this.inputName`や`this.inputPass`から値を取り出しメッセージを出力させています<br>
実行すると`input`に入力された内容が画面に表示されているかと思います。

img4

##### スタイルシート適用
****

`className`で指定した部分にスタイルシートを適用させてみます。<br>
と言っても普通のCSSと変わりないです。

```css
.login {
  background: #e0ffff;
}

.form {
  background: #fff8dc;
}
```

背景色を変更。リロードすると以下のようになります。

img5
