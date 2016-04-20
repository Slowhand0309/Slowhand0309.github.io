## Butterknifeを使ってみる 1

Androidでリソースや動作の紐づけ(DI)をアノテーションでやってくれる<br>[**Butterknife**](http://jakewharton.github.io/butterknife/)を使ってみます。

* 環境

```
IDE : AndroidStudio2.0
ビルドツール : gradle
```

#### インストール
***

build.gradleのdependenciesに
```
compile 'com.jakewharton:butterknife:7.0.1'
```
を追加するだけ。簡単^^


#### 実際に使ってみる
***

単純にEditTextのリソースとメンバ変数mEditTextを紐づけて値を設定してみます。

・レイアウトxml
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editText" />

</LinearLayout>
```

・MainActivity.java

```java
package com.app.sample.sampleapp;

import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.widget.EditText;

import butterknife.Bind;
import butterknife.ButterKnife;

public class MainActivity extends AppCompatActivity {

    @Bind(R.id.editText)
    EditText mEditText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);

        mEditText.setText("Sample");
    }
}
```

* 実行結果

img1

EditTextに値が設定されているのが分かるかと思います。<br>
やっている事としては、

```java
@Bind(R.id.editText)
EditText mEditText;
```
でレイアウトxmlで定義した
```xml
android:id="@+id/editText"
```
のidを指定して変数mEditTextに結び付けています。

次にonCreateで
```java
ButterKnife.bind(this);
```
をやる事で結び付けが行われます。

次にボタンをクリックした時の動作を設定してみたいと思います。

・レイアウト.xmlファイルにボタンを追加

```xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="Sample"
    android:id="@+id/buttonSample"
    android:layout_gravity="right" />
```

img2

このボタンをクリックした時の動作を以下のように設定してやります。

```java
@OnClick(R.id.buttonSample)
void onClickSample() {
    Toast.makeText(MainActivity.this, mMessage, Toast.LENGTH_LONG).show();
}
```
OnClickアノテーションでボタンのリソースIDを指定し、紐づけるメソッド
つまりボタンがクリックされた時実行されるメソッドを記述しています。

ここで出てきたmMessageですが、以下のようにしてstring.xmlに設定した文字を紐づけています
```java
@BindString(R.string.hello)
String mMessage;
```
・ボタンをクリックした時

img3

ん〜実に実装する手間が省けて便利です^^
