## Elasticsearchを学ぶ


[前回](http://developabout0309.blogspot.jp/2016/02/6-elasticsearch.html)サクッと触れてみましたが、
今回じっくり触ってみたいと思います。


#### 概用
***

Elasticsearchのデータ構造を[公式のドキュメント](https://www.elastic.co/guide/en/elasticsearch/reference/current/_basic_concepts.html#_node)を読んで自分なりにまとめてみました<br>
※間違っていたら教えて頂けると助かります。

* Cluster

  ・・・1つかそれ以上のNodeを持ち、複数のNodeをまたいで検索できる<br>クラスタにはユニークな名前をつける必要がある
* Node

  ・・・クラスタ内の要素、デフォルトの名前はMarvelのキャラクターの名前が付けられる<br>

* Index

  ・・・データの集合、集合の特徴を表す名前にする。例) customer, product

* Type

  ・・・マッピングタイプ。Index内の要素でデータのタグみたいなもの

* Document

  ・・・最小単位のデータ。DocumentのデータがJSON形式で表現される

* Shards

  ・・・一つのIndexに膨大なデータを登録する際などに、細分化する仕組み<br>実際はLuceneのインスタンス

図にするとこんな感じのイメージです。

img1

#### サンプルデータ投入
***

[公式ページ](https://www.elastic.co/guide/en/elasticsearch/reference/current/_exploring_your_data.html#_sample_dataset)にサンプルの顧客データが用意されているので、それを使おうと思います。

※公式ページのサンプルデータと同じリンクを貼っときます [here](https://github.com/bly2k/files/blob/master/accounts.zip?raw=true)

早速ダウンロードした`accounts.json`を登録してみたいと思います。<br>
`accounts.json`と同じディレクトリで以下コマンドを実行。

```sh
$ curl -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary "@accounts.json"
```
↑のコマンドでは`Index`がbank、`Type`がaccountにデータを登録します

* 実際に登録できたか確認

```sh
$ curl 'localhost:9200/_cat/indices?v'
health status index pri rep docs.count docs.deleted store.size pri.store.size
yellow open   bank    5   1       1000            0    442.1kb        442.1kb
```
`docs.count`を見ると1000件の`Document`が登録されているのがわかります。


#### 検索してみる
***

単純にaccount_numberが20のアカウントを検索してみます。

```sh
$ curl -XPOST 'localhost:9200/bank/_search?pretty' -d '
{
  "query": { "match": { "account_number": 20 } }
}'
```
検索時には[QueryDSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html)を使って検索します。

成功すれば以下のレスポンスが返ってくると思います。
```sh
{
  "took" : 20,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 5.6587105,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "20",
      "_score" : 5.6587105,
      "_source" : {
        "account_number" : 20,
        "balance" : 16418,
        "firstname" : "Elinor",
        "lastname" : "Ratliff",
        "age" : 36,
        "gender" : "M",
        "address" : "282 Kings Place",
        "employer" : "Scentric",
        "email" : "elinorratliff@scentric.com",
        "city" : "Ribera",
        "state" : "WA"
      }
    } ]
  }
}
```

それぞれのパラメータの意味は・・

* **took** - Elasticsearchが検索にかかった時間(ms)
* **timed_out** - タイムアウトしたかどうかの真偽値
* **_shards** 検索するのにどれだけの`Shards`を使用したか
* **hits** 検索結果
* **hits.total** 検索`Document`数
* **hits.hits** 検索結果の配列(デフォルトでは先頭の10件表示)
