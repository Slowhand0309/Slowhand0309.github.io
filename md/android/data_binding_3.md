## Android DataBinding を使ってみる3 - MVVMパターン -

[前回](http://developabout0309.blogspot.jp/2016/11/android-databinding-2.html)はイベントのBindingの基本的な所を<br>
やりましたが、今回はDataBindingを使ってMVVM(Model-View-ViewModel)をやってみます。

サンプルとしては、`EditText`に入力した内容を<br>
リアルタイムに`TextView`に反映させる定番のやつです。

実際の動作は↓のようになります。

![gif1](http://slowhand0309.github.io/images/blog/android/data_binding3/databinding3_1.gif)

#### 環境
****

Android Studio 2.2.2

#### 実装方法
****

まずはレイアウトファイルですが、↓のようになってます。
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
    <data>
        <variable name="field" type="com.example.slowhand.databindingsample.EditField"/>
    </data>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:padding="20dp"
        android:orientation="vertical">

        <EditText
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@={field.text}"/>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@{field.text}"/>

    </LinearLayout>
</layout>
```

ここで`EditText`の`@={field.text}`が通常の`@{field.text}`とは違って<br>
`=`が付いています。これは`Android Studio 2.1 Preview 3`から使える双方向のDataBindingです。

双方向というのが、getterだけでなく`EditText`でユーザーが変更した場合setterも呼ばれます。<br>
こうする事で入力した内容を即座にtextプロパティにセットし`TextView`で表示させる事が出来ます。

* EditFieldクラス

双方向を実現する為の実装としては2通りあります。<br>
まず`ObservableField`を使う方法。

```java
public class EditField {

    public final ObservableField<String> text =
            new ObservableField<String>();
}
```

こうする事で`text`が変更されると自動でUIに通知してくれます。

次に`BaseObservable`クラスを継承しオブザーバブル オブジェクトを作成する場合。
```java
public class EditField extends BaseObservable {

   @Bindable
   private String text;

   public String getText() {
       return text;
   }

   public void setText(String text) {
       this.text = text;
       notifyPropertyChanged(BR.text);
   }
}
```

`@Bindable`アノテーションをGetterまたはプロパティにセットし、<br>
`setText`が呼ばれる(EditTextが更新された)時に`notifyPropertyChanged(BR.text);`で<br>
通知をしています。
