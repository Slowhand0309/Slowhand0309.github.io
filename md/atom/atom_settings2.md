## Atomまとめ5

最近のAtom環境ですが、フロントエンドの画面周りの開発時などで<br>
個人的にかなり捗るとベストな環境を発見したので晒してみたいと思います。

## オススメなパッケージ組み合わせ

いつも`Rails`や`PhoneGap-CLI`など開発する際には、`livereload`させながら<br>
`html`や`css`,`javascript`など実装してるんですが、そういう場合に

* **[browser-plus](https://atom.io/packages/browser-plus)**
* **[emmet](https://atom.io/packages/emmet)**
* **[platformio-ide-terminal](https://atom.io/packages/platformio-ide-terminal)**

この3つのパッケージを入れとくと凄く捗ります:smile:

:point_down:実際に使ってる様子です

![gif](http://slowhand0309.github.io/images/blog/atom/atom_settings2.gif)

> browser-plus

[前回](http://developabout0309.blogspot.jp/2016/02/atom4.html)も紹介しましたが、さらに`livereload`を有効にするには

img1

このアイコンをクリックします。

> emmet

既に使ってる方もいるとは思いますが、例えば・・・
```html
<div id="hoge"><a href="" class="fuga"></a></div>
```
と書きたい時に
```
div#hoge>a.fuga
```
と書いてタブを押せばhtmlに展開してくれます:star2:便利

> platformio-ide-terminal

結構ターミナル系のパッケージ色々試しましたが、<br>
WindowsとMac両方そつなく使えるのを探していたら<br>
`platformio-ide-terminal`に出会いました

使い方はシンプルでエディッタ下部の`+`を押すとターミナルが出現します。<br>
また複数ターミナルを開けるので便利です。

![gif](http://slowhand0309.github.io/images/blog/atom/atom_settings2_2.gif)
