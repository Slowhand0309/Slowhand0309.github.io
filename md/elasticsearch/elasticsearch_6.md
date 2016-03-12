## Elasticsearchを学ぶ 6


今回はElastic社のKibanaを使ってみたいと思います。

> Kibanaとは?

ログデータなどの可視化ツール。フィルタや集計などを高速に便利に行う。

* 環境

引き続き[前回](http://developabout0309.blogspot.jp/2016/02/6-elasticsearch.html)の環境を前提に進めていきたいと思います。


### インストール
****

インストールの方法としては、[公式ページ](https://www.elastic.co/downloads/kibana)からダウンロードしてインストールします。

```sh
$ cd
$ wget https://download.elastic.co/kibana/kibana/kibana-4.4.1-linux-x64.tar.gz -O kibana.tar.gz
$ tar -zxf kibana.tar.gz
$ sudo mv kibana-4.4.1-linux-x64 /usr/local/share/kibana
$ sudo chmod -R 755 /usr/local/share/kibana
$ sudo chown -R vagrant:vagrant /usr/local/share/kibana
```

* 設定の変更

kibanaの設定ファイルを修正
```sh
$ cd /usr/local/share/kibana
$ vi config/kibana.yml
```
`elasticsearch.url`のコメントを外し、以下に設定。<br>
elasticsearch.url: "http://192.168.33.180:9200"

kibana用のポート`5601`をVagrantファイルに設定し再起動します

```ruby
# 以下設定をVagrantファイルに追加
config.vm.network "forwarded_port", guest: 5601, host: 5601
```

* 再起動

```sh
$ vagrant reload
```

* ElasticsearchとKibanaを起動

```sh
$ /usr/local/share/elasticsearch/bin/elasticsearch -d -p pid
$ /usr/local/share/kibana/bin/kibana
```

ブラウザからhttp://192.168.33.180:5601/ にアクセス

img1

↑のような画面が出ればOK

デフォルトの`Index`マッチパターンを作成しないと次に進めないので、<br>
とりあえずいかに設定し`Create`

img2

以下の画面のようにIndexパターンが作成される

img3


### 可視化
****

とにもかくにも何かグラフを表示させてみる<br>
`Visualize`タブを開き、`Line Chart`を選択

img4


`From a new search`を選択

img5

左側の設定を以下のように設定

img6

`▶︎`ボタンを押すとグラフが表示される

img7
