## Android Tips3

前回やったAndroid Design Support Library(ADSL)の

* Floating Action Button
* Snackbar
* Tabs

をやってみます。

#### Floating Action Button
***

画面上丸いボタンを表示させます。<br>
マテリアルデザイン的には画面で重要なアクションをこのボタンに紐付けるようになっています。

* シンプルなlayout.xml

```xml
<android.support.design.widget.FloatingActionButton
    android:id="@+id/fab"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="bottom|right"
    android:src="@android:drawable/ic_media_play"/>
```

* 表示結果

img1

小さめなサイズを表示させる場合は
```xml
<LinearLayout
 xmlns:app="http://schemas.android.com/apk/res-auto"
 ・・・

 <android.support.design.widget.FloatingActionButton
     android:id="@+id/fab"
     app:fabSize="mini"
     ・・・
```

#### Snackbar
***

下からニュッと出てくるやつです。<br>
シンプルに先ほどの`Floating Action Button`を押したら出てくるようにします。

```java
FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
fab.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Snackbar.make(findViewById(android.R.id.content),
                "Snackbar Sample", Snackbar.LENGTH_LONG).show();
    }
});
```

実際に動かしてみると。

![optimaize](http://slowhand0309.github.io/images/blog/android/snackbar1.gif)

Snackbarは下から出てきてますが、`Floating Action Button`が隠れています。<br>
そこで便利な`CoordinatorLayout`を使うと子Viewの位置やサイズを調整してくれます。

レイアウトを↓のように変更します。
```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    android:id="@+id/coordinator_layout"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">


    <android.support.design.widget.FloatingActionButton
        android:id="@+id/fab"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom|right"
        android:layout_marginBottom="20dp"
        android:layout_marginRight="20dp"
        app:fabSize="mini"
        android:src="@android:drawable/ic_media_play"/>

</android.support.design.widget.CoordinatorLayout>
```

ボタン押下時の実装

```java
FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
fab.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        // android.R.content -> R.id.coordinator_layoutに変更
        Snackbar.make(findViewById(R.id.coordinator_layout),
                "Snackbar Sample", Snackbar.LENGTH_LONG).show();
    }
});
```

実際に動かしてみるとボタンの位置を調節してくれているのが分かります。

![optimaize](http://slowhand0309.github.io/images/blog/android/snackbar2.gif)

またSnackbarの背景色やテキストの文字色も変更できます。
```java
FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
fab.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View view) {
        Snackbar bar = Snackbar.make(findViewById(R.id.coordinator_layout),
                "Snackbar Sample", Snackbar.LENGTH_LONG);
        bar.setActionTextColor(Color.RED);
        bar.getView().setBackgroundColor(Color.GREEN);
        bar.show();
    }
});
```

表示結果

img4
