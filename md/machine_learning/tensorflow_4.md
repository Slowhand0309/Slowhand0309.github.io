## 機械学習のフレームワークTensorflowを試す 4 - データフローグラフを理解する -

[前回](http://developabout0309.blogspot.jp/2016/05/tensorflow3.html)のサンプルコードでは事前に計算する方法を定義して、<br>
最後に実際の計算を行ってました。

これこそが**データフローグラフ**の形になります。

`データフロー`はデータの流れです。ではグラフはというと**データ構造**になります。<br>

img1

上の図のように、**ノード**とそのノード間を連結する**エッジ**で構成されます。<br>
ノードとノードの繋がり方に着目したものになります。

##### Tensorflowのデータフローグラフ
****

Tensorflowではノードを**計算操作(Operation)**としています。<br>
そしてノードが扱うデータ形式が前回も出てきた**テンソル(Tensor)**になります。

ここで例えば、配列`[3, 4, 5]`の2乗した値の平均値を算出してみます。<br>
通常のプログラムだと・・
```ruby
[3, 4, 5].map { |v| v *v }.inject(:+) / 3
# => 16
```
上のように、全ての要素を2乗し合計したものを要素数3で割って算出しています。<br>
これをTensorflowで計算させると・・

```python
$ python
>>> import tensorflow as tf
>>> array = tf.constant([3,4,5])
>>> square = tf.square(array)
>>> reduce = tf.reduce_mean(square)
>>> sess = tf.Session()
>>> ret = sess.run(reduce)
>>> print(ret)
16
```

上の例でのノードは`array`、`square`、`reduce`になります。<br>
`[3,4,5]`のデータは各ノードを通り、最終的に計算された値が出力されます。

##### セッション
****

実際に計算を行う為にTensorflowではセッションを作成します。<br>
```python
sess = tf.Session()
```
`run`メソッドにノードを渡すことで対象のノードを計算します。
```python
sess.run(reduce)
```

ちなみに上の`array`、`square`、`reduce`の各ノードを実行させると・・
```python
$ python
>>> sess = tf.Session()
>>> array = tf.constant([3,4,5])
>>> ret = sess.run(array)
>>> print(ret)
[3 4 5]
>>> square = tf.square(array)
>>> ret = sess.run(square)
>>> print(ret)
[ 9 16 25]
>>> reduce = tf.reduce_mean(square)
>>> ret = sess.run(reduce)
>>> print(ret)
16
```
となり、各ノード毎の計算が行なわれているのが確認できているかと思います。
