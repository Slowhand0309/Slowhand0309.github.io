## Atomまとめ4

久しぶりにAtomネタ

最近の入れてるパッケージや`style.less`を晒してみたいと思います。

↓現在Atomを使ってる様子。

![gif](http://slowhand0309.github.io/images/blog/atom/atom_preview.gif)

## パッケージ

現在インストールしているパッケージは以下の4つです。<br>
あんまし入れすぎると重くなるので、よく使う5つに絞って入れてます。

1. [minimap](https://atom.io/packages/minimap)
2. [minimap-hide](https://atom.io/packages/minimap-hide)
3. [highlight-selected](https://atom.io/packages/highlight-selected)
4. [browser-plus](https://atom.io/packages/browser-plus)
5. [file-icons](https://atom.io/packages/file-icons)

> minimap & minimap-hide

パネル内の左右どちらかにミニマップを表示します。<br>
`minimap-hide`はパネル分割時にアクティブじゃない方のミニマップを<br>
非表示にしてくれます。

> highlight-selected

選択したキーワードをハイライトしてくれます。<br>
自分は置換文字を選択する時にこれでハイライトにして、<br>
`command + d`で選択してます。結構便利です。

> browser-plus

パネル内にブラウザを表示させ使うことができます。<br>
調べながら書き物する時などに便利です。

> file-icons

ファイルのアイコンの見た目をゴージャスにしてくれます。


## style.less

Settings > Theme > your stylesheet を選択するとAtomの見た目を<br>
カスタマイズできる`style.less`が編集できます。

※ lessはスタイルシート(CSS)を動的に使えるようにしたものです

現在は↓の設定にしてます。
```css
// 全体のフォントサイズ
* {
  font-size:14px;
}

// Markdownプレビュー時のリンク貼ってる所を見やすくなるよう追加
.markdown-preview {
  a {
    &:link { color: #1F90FF; }
    &:visited { color: #DCDCDC; }
  }
}

// 左右のパネル分割で現在アクティブなパネルが分かるように
// 明暗をつける
.vertical {
  .pane {
    opacity:0.70;

    &.active {
      opacity: 1.0;
    }
  }
  // パネル分割時の境界線を色つきにする
  .panes .pane-row > *,
  .panes .pane-column > * {
    border-right: 1px solid rgba(255, 255, 255, 0.5);
  }
}
```
