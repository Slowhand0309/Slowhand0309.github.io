## PropertiesService

今回はデータをkey-value形式で管理してくれる`PropertiesService`について。<br />

<br />
データの共有範囲として、

* 全てのユーザーで現在のドキュメント間(add-onで公開しても共有)
* 全てのユーザーで現在のスクリプト間
* 現在のユーザーで現在のスクリプト間

の3つある。それぞれ専用のメソッドが用意されており

* getDocumentProperties()
* getScriptProperties()
* getUserProperties()

の3つある。それぞれ`Properties`クラスが返ってくる。<br />
また、別々のスクリプト間で共有はできない。

<br />
まあ、複数のドキュメント間では共有できないという事ですね

<br />

##### サンプル
***

早速サンプルを試してみたいと思います。<br />
使用するのは`getDocumentProperties()`

```javascript
function setup() {
  var dp = PropertiesService.getDocumentProperties();
  dp.setProperty('AAA', 'HOGE');
  dp.setProperty('BBB', 'FUGA');
}

function dump() {
  var dp = PropertiesService.getDocumentProperties();
  var keyA = dp.getProperty('AAA');
  var keyB = dp.getProperty('BBB');
  var keyC = dp.getProperty('CCC');

  Logger.log('AAA : ' + keyA);
  Logger.log('BBB : ' + keyB);
  Logger.log('CCC : ' + keyC);
}
```

単純ですが、`setup()`でデータをそれぞれのキーで保存し、<br />
`dump()`でデータを取り出しログ出力しています。

↓※実行する時には認証を求められます。

実行結果としては、

```
AAA : HOGE
BBB : FUGA
CCC : null
```

になります。<br />
ちなみに存在しないキーを設定すると'null'が返ってきます。

##### Propertiesクラス
***

↓`Propertiesクラス`で用意されているメソッドです。

* **deleteAllProperties()**

  現在のProperties設定値を全削除

* **deleteProperty(key)**

  現在のProperties設定値の`key`で設定されている内容を削除

* **getKeys()**

  現在のProperties設定値のkey配列`String[]`を取得

* **getProperties()**

  現在のPropertiesのコピーを取得

* **getProperty(key)**

  現在のPropertiesから引数で指定されたkeyの設定値を取得

* **setProperties(properties)**

  現在のPropertiesに引数のPropertiesを設定

* **setProperties(properties, deleteAllOthers)**

  引数のPropertiesを現在のPropertiesに設定し、
  引数のdeleteAllOthersが`true`の場合、設定された内容以外の設定値を削除

* **setProperty(key, value)**

  現在のPropertiesに引数のkeyをキーにvalueを設定する
