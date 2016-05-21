## 機械学習のフレームワークTensorflowを試す - 簡単なデモを試す -

[前回](http://developabout0309.blogspot.jp/2016/05/tensorflow1.html)は環境構築を行いましたが、今回は簡単なデモを実行してみたいと思います。

##### MNISTデータセットを使ったデモ
****

早速`Virtualenv`の環境に切り替えます。
```sh
$ source <特定のディレクトリ>/bin/activate
```

シンプルなデモが既にインストールされているのでそれを実行します。<br>

ちなみに`Tensorflow`がどこにインストールされているかは、
```sh
$ python -c 'import os; import inspect; import tensorflow; print(os.path.dirname(inspect.getfile(tensorflow)))'
```
で見つける事ができます。

手書きのMNISTデータセットを使ったシンプルなデモが上で見つけたパス配下の<br>
`models/image/mnist/convolutional.py`にあるのでそれを実行してみます。

MNISTデータセットは↓のようなものです。

img1

いざ実行してみます。
```sh
$ python -m tensorflow.models.image.mnist.convolutional
```
※ `python -m`を指定するとプログラムを見つけて実行してくれます

実行すると勝手にデータを取得して解析してくれます。
```sh
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting data/train-images-idx3-ubyte.gz
Extracting data/train-labels-idx1-ubyte.gz
Extracting data/t10k-images-idx3-ubyte.gz
Extracting data/t10k-labels-idx1-ubyte.gz
Initialized!
Step 0 (epoch 0.00), 6.7 ms
Minibatch loss: 12.054, learning rate: 0.010000
Minibatch error: 90.6%
Validation error: 84.6%
Minibatch loss: 3.269, learning rate: 0.010000
Minibatch error: 6.2%
Validation error: 6.9%
Step 200 (epoch 0.23), 399.2 ms
Minibatch loss: 3.474, learning rate: 0.010000
Minibatch error: 12.5%
Validation error: 3.6%
・・・中略・・・
Step 8500 (epoch 9.89), 357.7 ms
Minibatch loss: 1.614, learning rate: 0.006302
Minibatch error: 3.1%
Validation error: 0.8%
Test error: 0.8%
```

最初は高かったエラー率が学習の成果で低くなっていってるのが分かるかと思います。<br>
最後にエラーが0.8%までになっています。

とりあえずデモは実行できましたが、イメージがわかない・・ので<br>
もっと色々試してみようかと思います。
