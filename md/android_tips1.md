## Android Tips1

Android開発で役に立つかも?しれないTipsを書いて行こうと思います。

#### リソース名指定読み込み
***

例えば・・

* `res_hoge1.png`
* `res_hoge2.png`
* `res_hoge3.png`
<br />・・・
* `res_hoge20.png`

なんてリソースファイルがある場合、真面目にやると
```java
Drawable d1 = getResources().getDrawable(R.drawable.res_hoge1);
Drawable d2 = getResources().getDrawable(R.drawable.res_hoge2);
Drawable d3 = getResources().getDrawable(R.drawable.res_hoge3);
・・・
Drawable d20 = getResources().getDrawable(R.drawable.res_hoge20);
```

かなり面倒くさい。できればListや配列でまとめたい所。。<br />
そういう時には以下の方法が便利です(エラー等は考慮してません)

```java
List<Drawable> drawableList = new ArrayList<Drawable>();
for (int i = 0; i < 20; i++) {
  int id = getResources().getIdentifier("res_hoge" + i, "drawable", getPackageName());
  Drawable d = getResources().getDrawable(id);
  drawableList.add(d);
}
```

詳細

R.javaファイルのパスを指定し、読み込みたいリソースファイルのある内部クラス※1
を指定するとResouresIDが取得できます。<br />
※1 `drawable`や`string`や`layout`など[ここ](http://developer.android.com/reference/android/R.html)を参照

getResources().getIdentifier("リソースファイル名", "Rファイルでのクラス名", "パッケージ名");

読み込みに失敗した場合はid=0を返す

#### ソフトキーボード表示の切替
***
* 特定のウィジェットに対して

例えばEditTextにフォーカスが移ったとき、フォーカスが外れたときに
ソフトキーボードを表示、非表示したいことがあると思います。

その際にはEditTextのオブジェクトに対してsetOnFocusChangeListenerを
設定してやることでフォーカスの移動で動作を制御してやることができます。
このときInputMethodManagerオブジェクトを利用することで表示・非表示を行います。

```java
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
    EditText ed = (EditText)findViewById(R.id.ed);
    ed.setOnFocusChangeListener(new View.OnFocusChangeListener() {
        @Override
        public void onFocusChange(View v, boolean hasFocus) {
            InputMethodManager inputMethodManager = (InputMethodManager) getSystemService(Context.INPUT_METHOD_SERVICE);
            // フォーカスを受け取ったとき
            if(hasFocus){
                // ソフトキーボードを表示する
                inputMethodManager.showSoftInput(v, InputMethodManager.SHOW_FORCED);
            }
            // フォーカスが外れたとき
            else{
                // ソフトキーボードを閉じる
                inputMethodManager.hideSoftInputFromWindow(v.getWindowToken(),0);
            }
        }
    });
}
```

* Activity起動時に非表示にする場合

これは簡単でonCreateの中で以下メソッドを呼び出してやる

```java
getWindow().setSoftInputMode(
        WindowManager.LayoutParams.SOFT_INPUT_STATE_ALWAYS_HIDDEN);
```
