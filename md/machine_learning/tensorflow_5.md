## 機械学習のフレームワークTensorflowを試す 5 - データフローグラフを可視化 -

前回やった配列`[3, 4, 5]`の2乗した値の平均値を算出するデータフローグラフを<br>
可視化してみたいと思います。

可視化するに当たって**TensorBoard**というツールを使用します。<br>
Tensorflowをインストールすると使えます。

基本的な使い方は
```sh
$ tensorboard --logdir=<ログディレクトリのパス>
```
として実行し、ブラウザで`http://0.0.0.0:6006`にアクセスすると<br>
可視化されたものが見られます。

##### 可視化する為にコード修正
****

まずは修正前のコードです。
```python
# coding: UTF-8
import tensorflow as tf

array = tf.constant([3,4,5])
square = tf.square(array)
reduce = tf.reduce_mean(square)
sess = tf.Session()
ret = sess.run(reduce)
print(ret) # => 16
```

このコードを可視化する為にちょこっと修正します。

```python
# coding: UTF-8
import tensorflow as tf

array = tf.constant([3,4,5])
square = tf.square(array)
reduce = tf.reduce_mean(square)
sess = tf.Session()
ret = sess.run(reduce)
# 以下の行を追加
tf.train.SummaryWriter('report', sess.graph)
print(ret) # => 16
```

`tf.train.SummaryWriter`クラスを作成する行を追加のしています。<br>
上では第一引数でイベントファイルを出力するディレクトリ名を、<br>
第二引数でセッションのグラフを渡しています。

今回は**単純にグラフを表示させるだけ**です。

##### TensorBoardで確認
****

修正したコードを実行すると`report`ディレクトリが作られているかと思います。<br>
`--logdir`にreportディレクトリのパスを指定してTensorBoardを起動してみます。

↓ブラウザでアクセスし`GRAPH`のタグを表示させた結果です。

img1

それっぽいのが表示されています。<br>
丸が定数のノードで、楕円が計算処理を行うノードです。

次に各ノードをクリックして詳細を見てみます。<br>
まずは`Const`つまりは`配列の[3,4,5]`です。クリックすると

img2

右上に詳細が表示されています。`Attributes`にデータ型と値が確認できます。

次は`Square`をクリックしてみます。二乗の計算処理を行うノードです。

img3

Inputが先ほどの`Const`でOutputが`Rank`と`Mean`となっています。<br>
`Mean`は最終的な平均値を求める計算処理を行うノードというのは想像できるんですが、
他の`Rank`と`range`は、、?<br>

調べてみると、<br>
`tf.reduce_mean`は引数として`reduction_indices`と`keep_dims`<br>
の引数を与えることができるようです。

例)
```python
>>> import tensorflow as tf
>>> array = tf.constant([[3.,4.],[5.,6.],[7.,8.]])
>>> reduce = tf.reduce_mean(array)
>>> reduce0 = tf.reduce_mean(array, 0)
>>> reduce1 = tf.reduce_mean(array, 1)
>>> sess = tf.Session()
>>> ret = sess.run(reduce)
>>> ret0 = sess.run(reduce0)
>>> ret1 = sess.run(reduce1)
>>> print(ret)
5.5
>>> print(ret0)
[ 5.  6.]
>>> print(ret1)
[ 3.5  5.5  7.5]
```
`tf.reduce_mean`の第二引数で渡している`reduction_indices`の値が<br>
何も指定しない場合は、
`(3 + 4 + 5 + 6 + 7 + 8) / 6 = 5.5`となります。

**0**の場合、(3, 5, 7), (4, 6, 8)の平均値を求めます。<br>
`(3 + 5 + 7) / 3 = 5`, `(4 + 6 + 8) / 3 = 6`

**1**の場合、(3, 4)、(5, 6), (7, 8)の平均値を求めます。<br>
`(3 + 4) / 2 = 3.5`, `(5 + 6) / 2 = 5.5`, `(7 + 8) / 2 = 7.5`

どういった平均を求めるか設定してるようです。<br>

今回のサンプルでは`reduction_indices`に値を<br>
設定していない為、`[9,16,25](square)`が`(9 + 16 + 25) / 3 = 16.66...`<br>
として計算されています。

`Rank`は`tf.rank`という次元数を求めるもので、`range`は`tf.range`という<br>
その名の通り範囲を求めるものです。<br>
`reduction_indices`を指定しなかった場合のみ表示されるので、<br>
`tf.rank`と`tf.range`の結果を内部で使っているのではないか・・・と思います。

具体的にここで使ってる!!というのを見つけたかったのですが今回はここまで。
