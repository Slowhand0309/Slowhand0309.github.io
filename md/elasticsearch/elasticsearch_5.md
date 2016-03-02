## Elasticsearchを学ぶ 4 - Analyzer -

今回はMappingする際に設定できる`Analyzer`というものを触ってみたいと思います。

その前に・・・

[前回](http://developabout0309.blogspot.jp/2016/02/elasticsearch-3-mapping.html)作成した`mapping.json`の補足です

```json
"user_id":  {
  "type":   "string",
  "index":  "not_analyzed"
},
```

`user_id`のフィールドに対してtypeがstringでindexが**not_analyzed**となっています。<br>
この`not_analyzed`ですが構文解析せず完全一致で検索します。

例えば・・<br>
user_idに`ABCD-1234`というデータが入ってる場合に`not_analyzed`を指定せずにABCDで検索した場合<br>
`ABCD-5678`でも`EFGH-1234`でもヒットしてしまいます。<br>
ユニークなフィールドにはつけたほうがいいやつです。

次に・・
```json
"created":  {
  "type":   "date",
  "format": "strict_date_optional_time||epoch_millis"
}
```
ですが、JSONは日時のデータ型を持ってないので、以下の3つの方法で表現します。

1. 文字列 ('2016-01-01'や'2016/01/01 10:10:50'など)
2. 数値 (`1970/01/01 00:00:00`からの経過ミリ秒(UTC))
3. 数値 (`1970/01/01 00:00:00`からの経過秒(UTC))

↑の`strict_date_optional_time||epoch_millis`は**2**になります


### Analyzer
****

一つのanalyzerは一つの`Tokenizer`と0個以上の`TokenFilters`で構成されます。<br>
サンプルとして以下のMappingを`analyzer_test`というIndexに作成します。<br>
※このサンプルでは`TokenFilters`は使用してません。
```json
{
  "settings": {
      "analysis": {
          "analyzer": {
              "my_analyzer": {
                  "tokenizer": "my_tokenizer"
              }
          },
          "tokenizer": {
              "my_tokenizer": {
                  "type": "nGram",
                  "min_gram": "2",
                  "max_gram": "2",
                  "token_chars": [
                      "letter",
                      "digit"
                  ]
              }
          }
      }
  },
  "mappings": {
    "sample": {
      "properties": {
        "text": {
          "type": "string",
          "analyzer": "my_analyzer"
        },
        "subtext": {
          "type": "string",
          "analyzer": "my_analyzer"
        }
      }
    }
  }
}
```

`my_tokenizer`として設定している箇所で、`nGram`とありますが、<br>
これはN個文字を区切って検索するものです。ここでは`min_gram`と`max_gram`に<br>
2と設定しているので2文字毎区切って検索を行います。

早速`analyzer_test_mapping.json`として保存しインポート
```sh
$ curl -X POST 192.168.33.180:9200/analyzer_test -d @analyzer_test_mapping.json
```

ちゃんと設定できているか確認してみます。

```sh
$ curl -X GET '192.168.33.180:9200/analyzer_test/_mapping/sample/?pretty'
{
  "analyzer_test" : {
    "mappings" : {
      "sample" : {
        "properties" : {
          "subtext" : {
            "type" : "string",
            "analyzer" : "my_analyzer"
          },
          "text" : {
            "type" : "string",
            "analyzer" : "my_analyzer"
          }
        }
      }
    }
  }
}
```

ちゃんと設定できているので、データ登録して動作を確認してみます。

```sh
$ curl -X PUT 192.168.33.180:9200/analyzer_test/sample/1 -d '{
   "text" : "abcdefg", "subtext" : "123456789"
 }'
```

まずは、分割された2文字に対して検索。<br>
```sh
$ curl -XPOST '192.168.33.180:9200/analyzer_test/_search?pretty' -d
'{ "query": { "match": { "text": "cd" } } }'
{
  "took" : 3,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 1,
    "max_score" : 0.11506981,
    "hits" : [ {
      "_index" : "analyzer_test",
      "_type" : "sample",
      "_id" : "1",
      "_score" : 0.11506981,
      "_source" : {
        "text" : "abcdefg",
        "subtext" : "123456789"
      }
    } ]
  }
}
```

これはヒットしますが、1文字で検索してみると、
```sh
$ curl -XPOST '192.168.33.180:9200/analyzer_test/_search?pretty' -d
'{ "query": { "match": { "text": "c" } } }'
{
  "took" : 8,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "failed" : 0
  },
  "hits" : {
    "total" : 0,
    "max_score" : null,
    "hits" : [ ]
  }
}
```

最低2文字で単語を分割してる為こちらはヒットしません。<br>
結構細かくAnalyzerを調整できるので、チューニング次第でヒット率が上がったり下がったりしそうですね。
