## 機械学習のフレームワークTensorflowを試す - 環境構築 -

やっと重い腰を上げて機械学習をやってみようかと思います^^;<br>

2015年はよく目にしていた**`人工知能/機械学習/ディープラーニング`**ですが、<br>
(※以前[オススメの書籍](http://developabout0309.blogspot.jp/2016/02/4.html)でも紹介しましたが、読んで満足してそれっきりです・・)<br>
難しそうなイメージで後回しにしてましたが、何はともあれやってみよう!!と・・<br>
いつものように**トライ&エラー**でやってみたいと思います。

まず上で出てきたキーワードですが、違いをはっきり理解する為に調べてみました。<br>
Wikipediaによると・・

* **[機械学習](https://ja.wikipedia.org/wiki/機械学習)**

  人工知能における研究課題の一つで、人間が自然に行っている学習能力と同様の機能をコンピュータで実現しようとする技術・手法のことである。

* **[ディープラーニング](https://ja.wikipedia.org/wiki/ディープラーニング)**

  多層構造のニューラルネットワークの機械学習の事

* **[人工知能](https://ja.wikipedia.org/wiki/人工知能)**

  人工的にコンピュータ上などで人間と同様の知能を実現させようという試み、或いはそのための一連の基礎技術を指す。

となっています。人工知能の中の機械学習という分野があって、機械学習の中にディープラーニングがあるといった事ですね。


##### Tensorflow
****

[Tensorflow](https://www.tensorflow.org)

実際に機械学習をやってみるんですが、現在機械学習のフレームワークが複数存在してます。(TensorFlow, Caffe, Chainer・・など)

今回はその中で`Tensorflow`に挑戦してみたいと思います。

> Tensorflowとは?

2015年11月にオープン化されたGoogleの機械学習ライブラリ<br>
Googleのサービスでも使われている。

特徴としては`data flow graphs`というものを使って処理され<br>
CPUでもGPUでも走り、PythonやC++でも書ける。

##### 環境構築
****

MacOS(El Capitan)への環境構築となります。<br>
[公式ページ](https://www.tensorflow.org/versions/r0.8/get_started/os_setup.html)の通りに進めていきたいと思います。

* Pip インストール

  Pythonのパッケージマネージャである`pip`をインストールします。

```sh
$ sudo easy_install pip
```

* Virtualenv インストール

  仮想のPython実行環境を作成してくれる`Virtualenv`をインストールします。

```sh
$ sudo pip install --upgrade virtualenv
```

* Virtualenv環境作成

```sh
$ virtualenv --system-site-packages <特定のディレクトリ>
$ source <特定のディレクトリ>/bin/activate
```

* Tensorflowをインストール

```sh
$ pip install --upgrade https://storage.googleapis.com/tensorflow/mac/tensorflow-0.8.0-py2-none-any.whl
```

##### テスト
****

それではちゃんと環境構築されたかテストしてみたいと思います。<br>
`python`コマンドを実行しインタラクティブモードで以下を実行します。

```python
$ python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
Hello, TensorFlow!
>>> a = tf.constant(10)
>>> b = tf.constant(32)
>>> print(sess.run(a + b))
42
>>> exit()
```

上の様に表示されればOKです。
