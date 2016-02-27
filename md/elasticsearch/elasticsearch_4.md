## Elasticsearchを学ぶ 3 - Mapping -

今回はデータをElasticSearchにインポートする際に、どのデータがどの項目に入るか?<br>
そのデータは数値か？日付か？などなど、データインポートする際に必要な<br>`Mapping`作業を行ってみたいと思います。


* 環境

引き続き[前回](http://developabout0309.blogspot.jp/2016/02/6-elasticsearch.html)の環境を前提に進めていきたいと思います。


### Mapping用のJSONファイル作成
****

MySQLなどで言うところのテーブルCreate用のSQLを作成するような感じだと思います。

* 用語

> Mapping Types

Index内のTypeを定義するもの。<br>
メタ要素として`_index`, `_type`, `_id`, `_source`がある。<br>
またMappingTypes内にはフィールドとして`Properties`を持つ。

> Field datatypes

フィールドのデータタイプを定義する。<br>
主要なものとして`string`, `date`, `long`, `double`, `boolean`, `ip`などがある。<br>
JSONのフォーマットで階層を用いて`object`や`nested`というデータタイプもあるらしい。


サンプルとして[公式ページ](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html#mapping)を参考にシンプルなMappingを作成してみます。

```json
{
  "mappings": {
    "user": { *1
      "properties": { *2
        "name":     { "type": "string"  }, *3
        "age":      { "type": "integer" }  
      }
    },
    "book": {
      "properties": {
        "title":    { "type": "string"  },
        "description":     { "type": "string"  },
        "user_id":  {
          "type":   "string",
          "index":  "not_analyzed"
        },
        "created":  {
          "type":   "date",
          "format": "strict_date_optional_time||epoch_millis"
        }
      }
    }
  }
}
```

一応ユーザーと、そのユーザーが所有する書籍というイメージで作成してみました^^;<br>
`*1`が`MappingType`、`*2`がフィールド、`*3`がフィールドとデータタイプになります<br>

試しにこれを`user_books`というIndexに適用させてみたいと思います。<br>
↑のJSONファイルを`mapping.json`として保存し、保存した場所で以下コマンド実行。

```sh
$ curl -X POST 192.168.33.180:9200/user_books -d @mapping.json
```
`{"acknowledged":true}`が返って来れば成功<br>
早速 http://192.168.33.180:9200/_plugin/head/ にアクセスして確認してみます。

img1

ちゃんと`user_books`というIndexが作成されていれば成功です。

### テストデータ投入
****

試しに1件`user`のTypeのid=1にデータを投入してみます。

```sh
$ curl -X PUT 192.168.33.180:9200/user_books/user/1 -d '{
  "name" : "hoge",
  "age" : 20
}'
```

以下のようなレスポンスがあれば成功です。
```json
{
  "_index":"user_books",
  "_type":"user",
  "_id":"1",
  "_version":1,
  "_shards":
  {
    "total":2,
    "successful":1,
    "failed":0
  },
  "created":true
}
```

ちゃんと登録されているか確認します。

img2

ちゃんと登録されてますね。<br>
今回はここまで。
