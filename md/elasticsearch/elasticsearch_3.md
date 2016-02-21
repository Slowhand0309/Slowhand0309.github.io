## Elasticsearchを学ぶ 2


今回はElasticsearchの便利なプラグイン`ElasticSearch Head`を使ってみたいと思います。<br>
※ここで使用しているElasticsearchのバージョンは`2.x`です。<br>
 `1.x`とコマンドが若干違います。

* 環境

ここでは[前回](http://developabout0309.blogspot.jp/2016/02/6-elasticsearch.html)の環境を前提に進めていきたいと思います。

前回の`Vagrant`ファイルの設定は
```
config.vm.network "forwarded_port", guest: 9200, host: 9200
config.vm.network "private_network", ip: "192.168.33.180"
```
になっています。


### ElasticSearch Head
****

Elasticsearchをブラウザで操作、閲覧できるようにするプラグインです。<br>公式ページは[こちら](http://mobz.github.io/elasticsearch-head/)


* **インストール**

```sh
$ cd elasticsearch
$ bin/plugin install mobz/elasticsearch-head
```

* 外部からアクセスできるようにする

※今回はiptablesを無効化します(本当はちゃんと設定しないとですが^^;)

```sh
$ sudo service iptables stop
```

ブラウザからhttp://192.168.33.180:9200/_plugin/head/ にアクセス

img1

↑のような画面が出ればOK

* 使い方

|タブ|概用|
|:--|:--|
|Overview|横にIndex、縦にNodeを表示<br>↑の画面では1つのNodeが4つのShardsでIndexのbank構成<br>※Unssaignedはとりあえず無視(- -;)|
|Indices|Indexの一覧|
|Browser|Indexのデータをブラウズできる|
|Structured Query|Queryを構築でき、どんなJSONになるか確認出来る|
|Any Query|Request Methodを投げることができる|

* Browser

Documentをクリックすると詳細が表示されます

* Structured Query

↑ではaccount_numberが20のものを表示させています<br>
クエリーのJSONが確認出来るので便利です。

* Any Query


### 軽く使ってみて
****

一回一回`curl`でやらなくてもGUIで出来るのはやっぱり便利だなと思います。
