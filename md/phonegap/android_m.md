## Android MarshmallowのPermission問題をPluginで解決

Android 6.0(Marshmallow)からpermissionモデルが新しくなってます。

[公式ページ](https://developer.android.com/training/permissions/index.html)

Android 6.0未満はインストール時に必要なpermissionsを一括で<br>
同意し、アプリの実装も許可されているものとして実装してます。

が、

Android 6.0からはインストール後の初期状態は全て拒否されており、<br>
アプリを起動して必要になった時にユーザーに同意を求めます。


##### PhoneGapを使ってる場合
****

Javaでネイティブでゴリゴリ実装〜という場合は素直にユーザーの同意が<br>
必要になった場合に
```java
if (checkSelfPermission(Manifest.permission.READ_CONTACTS)
        != PackageManager.PERMISSION_GRANTED) {
   // ユーザーに許可を求める
   ・・・・
}
```
[参考URL](https://developer.android.com/training/permissions/requesting.html)<br>
といった具合に実装していかないといけませんが、PhoneGapなら<br>
`cordova-plugin-android-permissions`という便利なプラグインがっ!!


##### cordova-plugin-android-permissions
****

[Github](https://github.com/NeoLSN/cordova-plugin-android-permission)

Android6.0未満の場合はpermissionチェックで全て許可済みになります。<br>今後の為に入れておいて損は無いかな〜と。

> Install

```sh
$ phonegap cordova plugin add cordova-plugin-android-permissions
```

> 例)カメラのパーミッションが必要な場合

```js
var permissions = cordova.plugins.permissions;
permissions.hasPermission(permissions.CAMERA, checkPermissionCallback, null);

function checkPermissionCallback(status) {
  if(!status.hasPermission) {
    var errorCallback = function() {
      console.warn('Camera permission is not turned on');
    }

    permissions.requestPermission(
      permissions.CAMERA,
      function(status) {
        if(!status.hasPermission) errorCallback();
      },
      errorCallback);
  }
}
```

指定できるpermission一覧は[ここ](https://developer.android.com/reference/android/Manifest.permission.html?hl=zh-tw)で確認できます。
