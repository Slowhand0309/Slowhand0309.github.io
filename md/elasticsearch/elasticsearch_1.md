## 新しい技術を学んでみる 6 - Elasticsearch -

**Elasticsearch**

AWSのサービスと勘違いしそうな名前ですが、、、<br>
※すでにAmazon Elasticsearch ServiceというElasticsearchを使った<br>
サービスがあるんでさらにややこしい・・・

そんなElasticsearchを学んでいきたいと思います。

> Elasticsearchとは?

Luceneという全文検索エンジンを利用しているElastic社の元でOSSとして開発されてるサービス

・・・Lucene?,全文検索エンジン?<br>
調べてみました。

> 全文検索エンジンとは?

wikipediaによると・・・
```
全文検索（ぜんぶんけんさく、Full text search）とは、
コンピュータにおいて、複数の文書（ファイル）から特定の文字列を検索すること。
「ファイル名検索」や「単一ファイル内の文字列検索」と異なり、
「複数文書にまたがって、文書に含まれる全文を対象とした検索」という意味で使用される。
```
とあります。

> Luceneとは?

こちらもwikipediaによると・・・
```
Javaで記述された全文検索ソフトウェア、Javaのクラスライブラリとして提供される。
1000万ドキュメントくらいの規模まで1台のマシンで対応できる。
```

この`Lucene`を使ったものとして、大きく分けると
* [Apache Solr](http://lucene.apache.org/solr/)
* [Elasticsearch](https://www.elastic.co/products/elasticsearch)

があるとのこと。

#### とりあえず触ってみる
***

実際に使って雰囲気を掴んでみたいと思います。

今回も`Vagrant`と`itamae`で`centos`に環境を構築してみたいと思います。

```sh
$ vagrant init
$ bundle init
```

VagrantファイルとGemfileを以下に編集

* Vagrantに以下行を追加
```
config.vm.network "forwarded_port", guest: 9200, host: 9200
config.vm.network "private_network", ip: "192.168.33.180"
```

* Gemfileに以下行を追加
```
gem "itamae"
gem "dotenv"
```

まずはvagrantを起動
```
$ vagrant up
```

itamaeのレシピとして`main.rb`を作成

```ruby
# coding: utf-8
require 'dotenv'

Dotenv.load

%w(yum wget java-1.8.0-openjdk).each do |pkg|
  package pkg do
    action :install
  end
end

puts "version : #{ENV['EVARSION']}"
execute 'elasticsearch download' do
  command "wget https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/#{ENV['EVARSION']}/elasticsearch-#{ENV['EVARSION']}.tar.gz -O elasticsearch.tar.gz"
end

execute 'elasticsearch.tar.gz unzip' do
  command 'tar -zxf elasticsearch.tar.gz'
end

puts "install path : #{ENV['INSTALL_PATH']}"
execute "sudo mv elasticsearch-#{ENV['EVARSION']} #{ENV['INSTALL_PATH']}" do
  not_if "ls #{ENV['INSTALL_PATH']}"
end

execute "sudo chmod -R 755 #{ENV['INSTALL_PATH']}" do
  only_if "ls #{ENV['INSTALL_PATH']}"
end

execute "sudo chown -R #{ENV['EUSER']}:#{ENV['EUSER']} #{ENV['INSTALL_PATH']}" do
  only_if "ls #{ENV['INSTALL_PATH']}"
end

```

`.env`を作成
```
EVARSION=2.2.0
INSTALL_PATH=/usr/local/share/elasticsearch
EUSER=vagrant
```
※バージョンやインストール先などは置き換えて下さい

実行してみます。
```sh
$ bundle exec itamae ssh --vagrant main.rb
```
エラーが出た場合は`.env`の設定を見直してみて下さい

* Elasticsearch設定

```sh
$ vagrant ssh
$ cd /usr/local/bin/elasticsearch
$ vi config/elasticsearch.yml
$ diff config/elasticsearch.yml  config/elasticsearch.yml.org
58c58
< http.port: 9200
---
> # http.port: 9200

```

* 起動

```sh
$ /usr/local/share/elasticsearch/bin/elasticsearch -d -p pid
```

* 確認

```sh
$ curl -X GET http://localhost:9200/
{
  "name" : "D'Ken",
  "cluster_name" : "elasticsearch",
  "version" : {
    "number" : "2.2.0",
    "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
    "build_timestamp" : "2016-01-27T13:32:39Z",
    "build_snapshot" : false,
    "lucene_version" : "5.4.1"
  },
  "tagline" : "You Know, for Search"
}
```

上のように表示されればOK

* 停止

```sh
$ kill `cat pid`
```

* 触ってみて

インストールが簡単で`Restfull API`で〜となると扱いやすいですね。<br>
最近人気なのもわかる気がします。^^;<br>
今回はここまで。
