## Android Tips2

今回は[Android Design Support Library(ADSL)](http://android-developers.blogspot.jp/2015/05/android-design-support-library.html)を触ってみました

> Android Design Support Library(ADSL)とは?

Googleが採用しているマテリアルデザインを便利に使えるようにしたもの<br>
Android2.1以上から使える

主要なものとして

* Navigation View
* Floating labels for editing text
* Floating Action Button
* Snackbar
* Tabs

などなど、今回は`Navigation View`と`Floating labels for editing text`をやってみる

#### Navigation View
***

画面の端からスワイプさせたらメニューが表示されるアレ<br>
Android Support Libraryに`NavigationDrawer`として組み込まれてますが、<br>
`NavigationView`はさらに簡単に便利に使えるようにしたもの

Layoutファイルとしては`DrawerLayout`の子View要素として組み込む<br>
※↓公式ページより抜粋
```xml
<android.support.v4.widget.DrawerLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:fitsSystemWindows="true">

    <!-- your content layout -->

    <android.support.design.widget.NavigationView
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_gravity="start"
            app:headerLayout="@layout/drawer_header"
            app:menu="@menu/drawer"/>
</android.support.v4.widget.DrawerLayout>
```

NavigationViewの中で`app:headerLayout`にはヘッダー部分に表示したいレイアウトを指定<br>
`app:menu`にはリストメニューに表示したいメニューリソースを指定する

* layout/drawer_header.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="178dp"
    android:background="@color/colorPrimaryDark">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textAppearance="?android:attr/textAppearanceLarge"
        android:text="NavigationView Sample"
        android:id="@+id/textView_header"
        android:textColor="@android:color/white"
        android:layout_alignParentBottom="true"
        android:layout_alignParentStart="true"
        android:layout_marginStart="26dp" />
</RelativeLayout>
```

* menu/drawer.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single">
        <item
            android:id="@+id/drawer_item_1"
            android:icon="@android:drawable/ic_media_play"
            android:title="PLAY"/>
        <item
            android:id="@+id/drawer_item_2"
            android:icon="@android:drawable/ic_media_pause"
            android:title="PAUSE"/>
        <item
            android:id="@+id/drawer_item_3"
            android:icon="@android:drawable/ic_media_next"
            android:title="NEXT"/>
        <item
            android:id="@+id/drawer_item_4"
            android:icon="@android:drawable/ic_media_previous"
            android:title="PREVIOUS"/>
    </group>
</menu>
```

* 動かした様子

![optimaize](http://slowhand0309.github.io/images/blog/android/navigation_view.gif)

#### Floating labels for editing text
***

EditTextに`android.support.design.widget.TextInputLayout`をラップするだけですが、<br>
効果としてはEditTextに設定していたhintがふわっと上部に浮き上がります。<br>
百聞は一見に如かずなので、いか動作させている様子です。

* リソース

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_alignParentTop="true"
    android:layout_alignParentStart="true">

    <android.support.design.widget.TextInputLayout
        android:id="@+id/first_text_input_layout2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">
        <android.support.v7.widget.AppCompatEditText
            android:id="@+id/first_edit_text2"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:hint="hello_world1"/>
    </android.support.design.widget.TextInputLayout>

    <android.support.v7.widget.AppCompatEditText
        android:id="@+id/first_edit_text"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="hello_world2"/>

</LinearLayout>
```

* 動かした様子

![optimaize](http://slowhand0309.github.io/images/blog/android/textinput.gif)
