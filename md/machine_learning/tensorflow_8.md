## 機械学習のフレームワークTensorflowを試す 8 - ソフトマックス回帰 -

前回はMNISTのデータを見てみましたが、今回は続きをやっていきたいと思います。

前回MNISTの画像データを`[60000, 784]`のn次元配列として扱うと書きました、
そして画像に対応するラベルもテンソルとして扱います。

ラベルは画像が0〜9のどの数値を現しているかになります。画像枚数分(60000)あるので、<br>
`[60000, 10]`のn次元配列として表すことができます。

例) 3を表す場合<br>
```
[0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0]
```

##### Softmax Regressions (ソフトマックス回帰)
****

データをテンソルでどう扱うかは分かりましたが、実際に学習する方法として、<br>
MNISTの学習では**`Softmax Regressions (ソフトマックス回帰)`**を使用するようです。

> ソフトマックス回帰とは?

ここで回帰(regression)という言葉が出てきましたが、<br>
**回帰**とは**データから関数を導こうとする手法**になります。

という訳でソフトマックス回帰とは、<br>
**ソフトマックスというものを使って関数を導き出す**
です。<br>
次は**ソフトマックス**を調べてみます。

> ソフトマックスとは?

n次元実数ベクトル`X = (X1, ・・・ Xn)`を受け取って<br>
n次元実数ベクトル`Y = (Y1, ・・・ Yn)`を返す。<br>
ただし、`Yi = E^Xi / E^X1 + E^X2 + ・・・ E^Xn`

・・・・:question::question::question::question::question:<br>
ん〜分かったような分からないような^^;

> ソフトマッックス関数の性質

* `0 < Yi < 1`

* `Y1 + ・・・ + Yn = 1`

* Xの各成分の中でXiが大きい、Yiはほぼ1でYの他の成分はほぼ0

具体例でいうと
```
x = (10, 1, 2)
```
をソフトマックス関数にかけると・・・
```
y = (0.9999・・, 0.0001・・, 0.0002・・・)
```
になる。つまり一番大きい成分を1に近づけ、<br>
それ以外は0に近づけるといった感じでしょうか。

##### MNISTでソフトマックス回帰
****

話を戻してMNISTで実際にどうソフトマックス回帰を使うかというと、<br>

1. 入力された[0-9]の画像データを重みとバイアスを使ってラベルと照らし合わせながら、重要なピクセルとそうでないピクセルの重みを分布させる。


2. 分布された重みをソフトマックス関数を使って活性化する。

大雑把ですが、、、<br>
↑の2でソフトマックス関数を使って一番大きい成分を1に近づけ、それ以外は0に近づけると行った活性化させるような役割を行います。

次はサンプルコードを見ながら処理の流れを見てみたいと思います。
