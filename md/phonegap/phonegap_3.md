## PhoneGapを使用する 3 - SwiftでPlugin作成 -

今回はPhoneGapの特徴であるPluginをSwiftで作成してみたいと思います。<br>
とりあえず今回はiOSをターゲットにしたPluginを作成します。

> Pluginとは?

PhoneGapのプラグインでは、WebViewからネイティブの機能を使えるようにしたものです。<br>

#### Goal
****

プラグインを経由して、ネイティブ側でカメラを起動する所までやってみたいと思います。<br>
今回はPlugin経由でネイティブ内で実行する所を確認し、次回カメラ起動をやってみたいと思います。


#### ディレクトリとファイルを作成
****

まずはカスタムプラグイン用のディレクトリを作成します。
```sh
$ mkdir plugins/custom-plugin-camera
```

次に`plugin.xml`、`src/ios/CustomCamera.swift`, `www/CustomCamera.js`を作成します。<br>
ここまでのディレクトリ構成は
```sh
plugins
│
.
.
.
├── custom-plugin-camera
│   ├── plugin.xml
│   ├── src
│   │   └── ios
│   │       └── CustomCamera.swift
│   └── www
│       └── CustomCamera.js
│
├── android.json
├── fetch.json
└── ios.json
```
こんな感じです。

#### プラグイン実装
****

次に`plugin.xml`を以下のように修正します。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<plugin xmlns="http://apache.org/cordova/ns/plugins/1.0"
    id="custom-plugin-camera"
    version="0.1.0">
    <name>CustomCamera</name>
    <description>Custom Camera Plugin</description>
    <license>Apache 2.0</license>
    <keywords>camera</keywords>

    <!-- ① -->
    <js-module src="www/CustomCamera.js" name="CustomCamera">
        <clobbers target="CustomCamera" />
    </js-module>

    <!-- ② -->
    <platform name="ios">
        <config-file target="config.xml" parent="/*">
            <feature name="CustomCamera">
                <param name="ios-package" value="CustomCamera" />
            </feature>
        </config-file>

        <js-module src="www/CustomCamera.js" name="CustomCamera">
            <clobbers target="CustomCamera" />
        </js-module>

        <source-file src="src/ios/CustomCamera.swift" />
    </platform>
</plugin>
```

 ① <br>
ここではプラグインを使う時に呼ばれるjsのモジュールを定義します。<br>
ちなみに`<clobbers target="CustomCamera" />`は、プラグインを使う際に<br>
`CustomCamera`でアクセスができるようにする為のものです。

例えば、プラグインで`echo()`メソッドが定義されていた場合、<br>
```js
CustomCamera.echo();
```
でメソッドが実行できます。

 ② <br>
ここではiosプラットフォームに関するプラグインの設定になります。

`CustomCamera.js`を以下のように実装します。
```js
var exec = require('cordova/exec');

var CustomCamera = {

  start: function() {
    exec(null, null, 'CustomCamera', 'start', []);
  }
};
module.exports = CustomCamera;
```

次に`CustomCamera.swift`を実装します。
```swift
import Foundation

@objc(CustomCamera) class CustomCamera : CDVPlugin {

    func start(command: CDVInvokedUrlCommand) {
        print("exec from plugin")
    }
}
```
今回はただネイティブ側でprintするだけの実装です。

最後に`plugins/ios.json`に作成したプラグインを追加します。
```json
    .
    .
    .
    "cordova-plugin-whitelist": {
        "PACKAGE_NAME": "xxxxxxxxx"
    },
    "custom-plugin-camera": {
        "PACKAGE_NAME": "xxxxxxxxx"
    }
```

iOSプラットフォームを追加し、作成されたxcodeprojファイルを開きます。
```sh
$ phonegap cordova platform add ios
$ open platforms/ios/XXXX.xcodeproj
```

Xcode上で実行し、コンソールに`exec from plugin`が表示されていれば成功です。
