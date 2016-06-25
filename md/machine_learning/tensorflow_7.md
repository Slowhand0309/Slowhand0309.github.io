## 機械学習のフレームワークTensorflowを試す 7 - MNIST事始め -

今回からは、機械学習の`Hello World`という事らしい、`MNIST`をやっていきたいと思います:beginner:<br>
[以前の記事](http://developabout0309.blogspot.jp/2016/05/tensorflow2.html)でデモを実行してはいますが、
今度は実際に学びながらやっていきます。

「[MNIST For ML Beginners](http://www.tensorflow.org/tutorials/mnist/beginners/index.html)」を元に進めて行こうと思います。

##### MNISTでどんな機械学習をさせるか
****

以前も書きましたが、MNISTは手書きの数字の画像のデータセットです。

<a href="https://1.bp.blogspot.com/-pCcBZAtjj5E/V0BLL1IhVGI/AAAAAAAAOOM/eDyTT1oq9HAkN916JY_nDbVPdR_q4gVlACLcB/s1600/img1.png" imageanchor="1" style="background-color: transparent; clear: left; display: inline !important; margin-bottom: 1em; margin-right: 1em; text-align: center;"><img border="0" height="223" src="https://1.bp.blogspot.com/-pCcBZAtjj5E/V0BLL1IhVGI/AAAAAAAAOOM/eDyTT1oq9HAkN916JY_nDbVPdR_q4gVlACLcB/s400/img1.png" width="400" /></a>

↑は0〜9まで色んなタッチで書かれてある。というのは人間からしたら簡単に予想がつきます。<br>
ですがコンピュータからすれば数字が書かれてあるなんて分からないので<br>
学習してもらって、何の数字が書いてあるか解析してもらおうと思います:sparkles:

##### MNISTデータ
****

使用するMNISTデータは[Yann LeCun's website](http://yann.lecun.com/exdb/mnist/)で提供されているので、<br>
そちらのデータを使用します。

一度ダウンロードしてデータセットがどんなものか見てみたいと思います。

提供元のページにいき、↓の4つをダウンロードし解凍します。

img2

それぞれ

* **手書き数字の画像データ**
* **手書き数字に対応するラベルデータ**
* **テスト用の画像データ**
* **テスト用の画像に対応するラベルデータ**

となっています。

> データ構造

* train-images-idx3-ubyte.gz

  ファイルには28×28ピクセルの画像が60000枚分書き込まれてます。<br>
  ・ファイルフォーマット
  ```
  [offset] [type]          [value]          [description]
  0000     32 bit integer  0x00000803(2051) magic number
  0004     32 bit integer  60000            number of images
  0008     32 bit integer  28               number of rows
  0012     32 bit integer  28               number of columns
  0016     unsigned byte   ??               pixel
  0017     unsigned byte   ??               pixel
  ........
  xxxx     unsigned byte   ??               pixel
  ```
  ※提供元ページから抜粋

* train-labels-idx1-ubyte.gz

  ファイルには画像に対応するラベルが60000個書き込まれてます。<br>
  ・ファイルフォーマット
  ```
  [offset] [type]          [value]          [description]
  0000     32 bit integer  0x00000801(2049) magic number (MSB first)
  0004     32 bit integer  60000            number of items
  0008     unsigned byte   ??               label
  0009     unsigned byte   ??               label
  ........
  xxxx     unsigned byte   ??               label
  ```
  ※提供元ページから抜粋

##### MNISTデータ読み込み
****

最終的には、画像データを読み込んでテンソルに格納します。<br>
テンソルは[60000, 784(28*28)]の2次元配列で格納します。

今回は、ファイル読み込む所までやってみます。

```python
import numpy as np

data = open("train-images.idx3-ubyte","rb")
dt = np.dtype("int32").newbyteorder('>')
# 1.
info = np.frombuffer(data.read(16), dtype=dt)
print(info) # => [ 2051 60000    28    28]

size = info[1] * info[2] * info[3]
dt = np.dtype("int8").newbyteorder('>')
# 2.
imageBuff = np.frombuffer(data.read(size), dtype=dt)
print(len(imageBuff)) # => 47040000
```

`1.`では先頭から16バイト分読み込んで、画像の情報を出力しています<br>
また、`2.`では読み込んだ画像データのサイズを出力しています。
