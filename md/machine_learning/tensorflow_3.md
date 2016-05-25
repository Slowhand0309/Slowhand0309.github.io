## 機械学習のフレームワークTensorflowを試す 3 - サンプルコードを紐解く -

前回まで環境設定と動かすまでやったので、今回から簡単なサンプルで<br>
色々学習していきたいと思います。

##### サンプルコード
****

公式ページのIntroductionに掲載されているコードです。
```python
import tensorflow as tf
import numpy as np

# Create 100 phony x, y data points in NumPy, y = x * 0.1 + 0.3
x_data = np.random.rand(100).astype(np.float32)
y_data = x_data * 0.1 + 0.3

# Try to find values for W and b that compute y_data = W * x_data + b
# (We know that W should be 0.1 and b 0.3, but Tensorflow will
# figure that out for us.)
W = tf.Variable(tf.random_uniform([1], -1.0, 1.0))
b = tf.Variable(tf.zeros([1]))
y = W * x_data + b

# Minimize the mean squared errors.
loss = tf.reduce_mean(tf.square(y - y_data))
optimizer = tf.train.GradientDescentOptimizer(0.5)
train = optimizer.minimize(loss)

# Before starting, initialize the variables.  We will 'run' this first.
init = tf.initialize_all_variables()

# Launch the graph.
sess = tf.Session()
sess.run(init)

# Fit the line.
for step in xrange(201):
    sess.run(train)
    if step % 20 == 0:
        print(step, sess.run(W), sess.run(b))

# Learns best fit is W: [0.1], b: [0.3]
```

`y = x * 0.1 + 0.3`の数式の内、`0.1`と`0.3`を隠してTensorflowに<br>
導きだしてもらうサンプルです。

##### 事前準備
****

まず、最初にxの値をランダムに100個作成し
```python
x_data = np.random.rand(100).astype(np.float32)
```
その値を元に数式を計算し`y_data`に格納します。
```python
y_data = x_data * 0.1 + 0.3
```
この時点でy_dataには100個ランダムな値が格納されています。

次の
```python
W = tf.Variable(tf.random_uniform([1], -1.0, 1.0))
b = tf.Variable(tf.zeros([1]))
y = W * x_data + b
```
ですが、`Variable`はメモリにテンソルをバッファします。

> テンソルとは・・?

学習処理で扱われるデータ構造。n次元の配列。

`tf.random_uniform([1], -1.0, 1.0)`では一次元の配列の要素1つ`[1]`に<br>
-1.0から1.0までの間のランダムなテンソルを返します。

`tf.zeros([1])`では一次元の配列の要素1つに0を設定したテンソルを返します。

次に
```python
loss = tf.reduce_mean(tf.square(y - y_data))
optimizer = tf.train.GradientDescentOptimizer(0.5)
train = optimizer.minimize(loss)
```
では、`tf.square(y - y_data)`で`y - y_data`の2乗した値が設定されたテンソル(1次元で100個の要素)を`tf.reduce_mean`で平均値を算出しています。
つまり元の正解の値(0.1と0.3)で計算した`y_data`とランダムな値で<br>
計算した`y`とを比較し間違いの度合いを算出しています。

`tf.train.GradientDescentOptimizer`で`最急降下法`のアルゴリズムを<br>
使用し間違い度合いが少なくなるように設定します。

この方法を`最小二乗法`というらしいです。。<br>
ちなみに何故二乗するかは、誤差がマイナスでもプラスでも二乗する事で全てプラスになります。

##### 学習の実行
****

最後に
```python
init = tf.initialize_all_variables()

# Launch the graph.
sess = tf.Session()
sess.run(init)

# Fit the line.
for step in xrange(201):
    sess.run(train)
    if step % 20 == 0:
        print(step, sess.run(W), sess.run(b))
```

学習を実行させ、適当なステップ毎に値を表示させています。<br>
実行する度に表示される値は異なりますが、だいたい`0.1`と`0.3`に近づいているかと思います。
