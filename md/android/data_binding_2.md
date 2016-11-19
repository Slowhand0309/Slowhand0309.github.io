## Data Binding を使ってみる2 - イベント処理 -

今回はイベント処理のBindingです。<br>
シンプルなクリック時のイベントをBindingさせる方法は以下の2通りです。

* メソッド参照

  クラス内のメソッドを参照し、イベントとしてBindingさせます。

* リスナーバインディング

  イベント発生時に実行するBinding

#### メソッド参照
****

ボタンをクリックしたら↓の`Handlers`クラスの`onClickEvent`メソッドが呼ばれるようにします。

```java
package com.example.slowhand.databindingsample;

import android.util.Log;
import android.view.View;

public class Handlers {

    private static final String TAG = "Handlers";

    public void onClickEvent(View view) {
        Log.d(TAG, "onClickEvent");
    }
}
```

メソッドの書式としては引数に`View`を取るメソッドを定義します。<br>
ちなみに`View`を引数に取らなかったり、そもそも無いメソッドを定義すると<br>
以下のようなエラーが発生します。
```
Error:(16, 32) Listener class android.view.View.OnClickListener with method onClick did not match signature of any method handlers.XXXX
Error:Execution failed for task ':app:compileDebugJavaWithJavac'.
> java.lang.RuntimeException: Found data binding errors.
```

レイアウトファイル(activity_main.xml)は以下のようになります。
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable name="handlers" type="com.example.slowhand.databindingsample.Handlers"/>
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="20dp"
        android:orientation="vertical">
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/app_name"
            android:onClick="@{handlers.onClickEvent}"/>

    </LinearLayout>
</layout>
```

Activity側ではレイアウトファイル(activity_main.xml)用のBindingクラスを取得し、<br>
`<variable>`で定義した`handlers`をセットしてやります。
```java
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        binding.setHandlers(new Handlers());
    }
}
```

実行結果

![gif1](http://slowhand0309.github.io/images/blog/android/data_binding2/databinding2_1.gif)

#### リスナーバインディング
****

メソッド参照と似てますが、レイアウトファイル内にラムダ式で表現できます。<br>
↑と同じ事をリスナーバインディングで書くと、

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable name="handlers" type="com.example.slowhand.databindingsample.Handlers"/>
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="20dp"
        android:orientation="vertical">

        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@string/app_name"
            android:onClick="@{(v) -> handlers.onClickEvent(v)}"/>

    </LinearLayout>
</layout>
```

このように書けます。<br>
式内では、特定の演算子が使えたりするので、

```xml
<Button
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="@string/app_name"
    android:onClick="@{(v) -> XXXX ? handlers.onClickEvent(v) : void}"/>
```

このように何らかの条件でイベント発生の切り替えが出来たりします。<br>
イベントを発生したく無い場合は`void`を指定します。
