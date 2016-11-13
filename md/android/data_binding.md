## Data Binding を使ってみる

Data Bindingは[以前](http://developabout0309.blogspot.jp/2016/04/butterknife-1.html)解説したButterknifeと同等の機能を持つ<br>
公式のライブラリーです。

#### インストール
****

まずは、サンプルのプロジェクトを作成したいと思います。<br>
プロジェクトの構成は以下の様にしました。

* 起動時のActivityクラス

  **MainActivity**

* レイアウトファイル

  **activity_main.xml**

* パッケージ名

  **com.example.slowhand.databindingsample**

プロジェクトが作成されたら、appの`build.gradle`に以下を追加します。
```gradle
android {
    ....
    dataBinding {
        enabled = true
    }
}
```

あとは`Gradle Sync`して完了です。簡単:sparkles:

#### 実装と動作確認
****

例えば、↓のようなレイアウトファイル(activity_main.xml)の場合
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:id="@+id/text_view1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <TextView
        android:id="@+id/text_view2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

    <TextView
        android:id="@+id/text_view3"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

</LinearLayout>
```

* ButterKnifeの場合

```java
@Bind(R.id.text_view1)
TextView mTextView1;

@Bind(R.id.text_view2)
TextView mTextView2;

@Bind(R.id.text_view3)
TextView mTextView3;
```

このように実装してリソースIDと変数を結びつけます。

* Data Bindingの場合

まずレイアウトファイルの要素を`<layout>`で囲ってやります。
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
  <LinearLayout
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:orientation="vertical">
      ・・・省略・・・
  </LinearLayout>
</layout>
```

次にActivity側でレイアウトファイル(activity_main.xml)に対応した<br>
ActivityMainBindingクラスが作成されるので、それを使って<br>
値を設定します。※レイアウトファイル名に依存

```java
public class MainActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        binding.textView1.setText("hoge1");
        binding.textView2.setText("hoge2");
        binding.textView3.setText("hoge3");
    }
}
```

動作結果

![img1](http://slowhand0309.github.io/images/blog/android/data_binding/img1.png)

TextViewに値が反映されています。

次はデータクラス(POJO)をバインドさせてみたいと思います。

```java
public class Item {

    private String mId;
    private String mName;
    private String mValue;

    public void setId(String id) {
        mId = id;
    }

    public String getId() {
        return mId;
    }

    public void setName(String name) {
        mName = name;
    }

    public String getName() {
        return mName;
    }

    public void setValue(String value) {
        mValue = value;
    }

    public String getValue() {
        return mValue;
    }
}
```

例えば上の様なクラスの値をTextViewに反映させたい場合、<br>
レイアウトファイルを以下の様に変更します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable name="item" type="com.example.slowhand.databindingsample.Item"/>
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <TextView
            android:id="@+id/text_view1"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{item.id}"/>

        <TextView
            android:id="@+id/text_view2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{item.name}"/>

        <TextView
            android:id="@+id/text_view3"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{item.value}"/>

    </LinearLayout>
</layout>
```

```xml
<data>
    <variable name="item" type="com.example.slowhand.databindingsample.Item"/>
</data>
```

`<variable>`で作成したデータクラスを定義し、`<data>`で囲みます。<br>
すると`@{item.value}`としてレイアウト内で使える様になります。

動作結果

![img2](http://slowhand0309.github.io/images/blog/android/data_binding/img2.png)
