## 機械学習のフレームワークTensorflowを試す 6 - さらに色々可視化する(TensorBoard) -

[前回](http://developabout0309.blogspot.jp/2016/06/tensorflow-5-tensorboard.html)やった可視化をさらに進めてみたいと思います。

##### 変数を可視化
****

前回は`tf.constant`で[3,4,5]のテンソル値を宣言していたのですが、<br>
今回は可視化する為に`tf.Variable`メソッドを使って宣言します。

修正したコードがこちら

```python
# coding: UTF-8
import tensorflow as tf

with tf.Graph().as_default():
    array_var = tf.Variable(tf.zeros([3]), name="array")
    square = tf.square(array_var)
    reduce = tf.reduce_mean(square)

    init = tf.initialize_all_variables()
    sess = tf.Session()
    sess.run(init)

    tf.merge_all_summaries()
    summary_writer = tf.train.SummaryWriter('report', sess.graph)

    ret = sess.run(reduce, feed_dict={array_var: [3.0, 4.0, 5.0]})
    print(ret) # => 16.6667
```

今回結果が`16.6667`となってますが、これは`tf.Variable`で<br>
`tf.zeros([3])`を使っている為です。

`tf.zeros`は指定しないと`tf.float32`でオール0のテンソルを作成します。<br>
`tf.float32`で計算する為、結果も少数値になってます。

値は、`sess.run`で`feed_dict`に[3,4,5]を指定し、`array_var`に値を設定しています。

##### TensorBoardで確認
****

それでは実際に`TensorBoard`を起動して確認してみたいと思います。

img1

真ん中に`name="array"`で指定した`tf.Variable`が表示されています。

クリックすると内容が見れたりします。

img2

##### スコープ付け
****

処理が複雑になってくると可視化も見づらくなってくるので、<br>
そんな時の為に`スコープ`を設定してみたいと思います。

実際にスコープを付けてみたソースがこちら

```python
# coding: UTF-8
import tensorflow as tf

with tf.Graph().as_default():
    with tf.name_scope('variable') as scope:
        array_var = tf.Variable(tf.zeros([3]), name="array")

    with tf.name_scope('op') as scope:
        square = tf.square(array_var)
        reduce = tf.reduce_mean(square)

    with tf.name_scope('init') as scope:
        init = tf.initialize_all_variables()
        sess = tf.Session()
        sess.run(init)

    tf.merge_all_summaries()
    summary_writer = tf.train.SummaryWriter('report', sess.graph)
    ret = sess.run(reduce, feed_dict={array_var: [3.0, 4.0, 5.0]})
    print(ret)
```

TensorBoardを起動して見てみると指定したスコープで<br>
区切られているのが分かるかと思います。

img3
