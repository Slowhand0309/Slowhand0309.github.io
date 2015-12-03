## Formの回答先を設定する

通常はFormを新規に作成した場合、回答先のスプレットシートは
設定しない限り回答用のスプレットシートが作成される。

> Formを新規作成

<br>
> 回答先の設定

Formの「回答を表示」メニューから、回答先を選択するダイアログが表示される。



##### 今回は回答先の設定をスクリプト上で行う
***

* GASを実行する用のスプレットシートを作成

* スクリプトエディッタ起動し以下内容で編集

```javascript
/**
 * スプレットシートとフォームの結び付け
 */
function bindForm() {

  // フォーム取得
  var formUrl = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXXX';
  var form = FormApp.openByUrl(formUrl);

  // スプレットシート取得
  var ss = SpreadsheetApp.getActiveSpreadsheet();
  form.setDestination(FormApp.DestinationType.SPREADSHEET, ss.getId());

  var date = new Date();
  var sheetName = date.getFullYear() + '/' + (date.getMonth() + 1) + '/' + date.getDate();

  // シート名を変更
  var sheets = ss.getSheets();
  for (var i = 0; i < sheets.length; i++) {
    // シート名に「フォームの回答」が含まれている場合
    var name = sheets[i].getName();
    if (name.indexOf('フォームの回答') != -1
        && name.indexOf(sheetName) == -1) {
        sheets[i].setName(sheetName);
    }
  }
}
```
※ FormのURLはFormエディッター画面のURLをコピー

* bindFormを実行する

初回は認証の承認が求められる

正常に処理が終了するとスプレットシートに新たなシートが追加されていると思います。

##### Formと回答スプレットシートのリンク解除
***
