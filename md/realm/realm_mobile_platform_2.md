## Realm mobile platform アプリ作成 - Android -

[前回](http://developabout0309.blogspot.jp/2016/10/realm-mobile-platform-1-ios.html)、RealmサーバーにiOSで作成したアプリと同期が取れている事を確認しました。<br>
今回はAndroidアプリを作成し、iOS->Realmサーバー->Androidで同期を取ってみたいと思います。

とりあえず今回はログインまでをやってみます。

#### Androidアプリ開発準備
****

> 開発環境

Android Studio 2.2.2<br>

###### プロジェクト作成&Realmインストール
<br>
まずはプロジェクトを作成。minSdkVersionは`19`、targetSdkVersionは`23`、<br>
Activity追加ではEmpty Activityを選択しました。

img1

早速`Realm`をインストールしていきます。

まずはトップにある`build.gradle`のdependenciesに↓を追加。

```java
dependencies {
    classpath "io.realm:realm-gradle-plugin:2.0.2"
}
```

次に`app/build.gradle`に↓を追加します。

```java
apply plugin: 'realm-android'

realm {
    syncEnabled = true
}
```
※`syncEnabled = true`にしないと`ObjectServer`関連が使えないので注意!!

最後に`build.gradle`を修正した際に右上に表示される**Sync Now**をクリック。<br>
Realmがインストールされます。

img2

#### ログイン画面作成
****

Realmサーバーにログインするまでを実装してみたいと思います。

* Applicationクラス作成

Realm関係の初期化を行う為に`Applicationクラス`を継承した<br>
クラスを作成します。今回は`MyApplicationクラス`にします。

作成したMyApplicationクラスの`onCreate`を以下のようにオーバーライドします。

```java
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        Realm.init(this);
    }
}
```

次に`AndroidManifest.xml`に`android:name=".MyApplication"`を追加します。

```xml
<application
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:name=".MyApplication" // ☆ここ
    android:theme="@style/AppTheme">
    <activity android:name=".MainActivity">
      ・・・
    </activity>
</application>
```

とりあえず今回は`MainActivity`の`onCreate`でログイン処理を実施します。

```java
public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        // [1]
        Credentials creds = Credentials.usernamePassword("ユーザー名", "パスワード", false);

        String authUrl = "http://10.0.2.2:9080/auth"; // [2]
        User.Callback callback = new User.Callback() {
            @Override
            public void onSuccess(User user) {
                Log.i(TAG, "Login success.");
            }

            @Override
            public void onError(ObjectServerError error) {
                String errorMsg;
                switch (error.getErrorCode()) {
                    case UNKNOWN_ACCOUNT:
                        errorMsg = "Account does not exists.";
                        break;
                    case INVALID_CREDENTIALS:
                        errorMsg = "User name and password does not match";
                        break;
                    default:
                        errorMsg = error.toString();
                }
                Log.e(TAG, errorMsg);
            }
        };
        User.loginAsync(creds, authUrl, callback);
    }
}
```

[1]の`usernamePassword`メソッド第三引数をtrueにするとユーザー新規作成<br>
falseにするとログインになります。

[2]で`http://10.0.2.2:9080/auth`としてますが、<br>
これはエミュレータ上で`localhost`にアクセスするには`10.0.2.2`に<br>
する必要がある為、このIPアドレスにしています。

この状態で`Realm Object Server`を立ち上げて実行し、<br>
ログに「Login success.」が表示されればOKです。
