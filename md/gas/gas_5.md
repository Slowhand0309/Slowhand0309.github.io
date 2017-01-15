## GASをES6で書く時に気にすること

個人用のSlackに執事的なボットをGASで作って、<br>
色々やってもらおうと思ってES6で作りました。

**[slack-butler](https://github.com/Slowhand0309/slack-butler)**

相変わらず名前のセンスは無く、まんまです・・・

この時に気をつけた事を書いてみようかと思います。

### 前提
****

ES6で書いて[webpack](http://webpack.github.io)を使って[Babel](https://babeljs.io)でES5に変換<br>
[node-google-apps-script](https://www.npmjs.com/package/node-google-apps-script)でGoogle Drive上にアップしてます。

### 「関数を選択」に関数が出てこない!!
****

これ↓

img1

普通に次のような関数を定義すると
```js
function main() {
  ...
}
```
関数を選択のドロップダウンに`main`が出てきますが、<br>
今回の場合BabelでES5に変換する為うまく出てきません:scream:

```js
global.main = function() {
  ...
}
```

Google Drive上にアップした後に`function main() ~`とか書くと<br>
当たり前ですが選択項目に出てきます。が、毎回これやるのも...

そんな時は**[gas-webpack-plugin](https://www.npmjs.com/package/gas-webpack-plugin)**が便利です

`npm install --save-dev gas-webpack-plugin`して、<br>
`webpack.config.js`に

```js
var GasPlugin = require("gas-webpack-plugin");
module.exports = {
  ...
  plugins: [
    new GasPlugin()
  ],
  ...
}
```
こんな感じで追加してやると、`function main() ~`を作ってくれます!!

### ウェブアプリケーションとして導入しても反映されない!!
****

こっから選択してGASをウェブアプリとして公開するやつです。

img2

ES6に関わらずですが、何回やっても反映されない場合は次を試してみて下さい

「ファイル」>「版を管理」を選択

img3

「新しいバージョンを保存」を選択し版数を増やします。

img4

再度ウェブアプリケーションとして導入を開き、<br>
プロジェクトバージョンに先ほどの版数を選択します。

img5

これで最新のコードが反映されてると思います


とまあ、とりあえず気にする事を書いてみましたが<br>
他にも気にする事があるって方はコメント頂けると幸いです。
