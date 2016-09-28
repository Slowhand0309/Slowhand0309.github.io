## Xcode小ネタ集 1

備忘録としての小ネタ集です。<br>

普段Xcodeばっかり触っていたら忘れないかもしれませんが、<br>
久しぶりに触るとすっかり忘れてしまうので・・


#### 複数のXcode切り替え
***

開発中などで複数のXcodeを切り替えて使う時用です。

`xcrun`などコマンドツールなどを使わない場合は、単純に<br>
現在のXcodeをリネームして[ここ](https://developer.apple.com/download/more/)からお目当のバージョンの<br>
Xcodeをダウンロードして使います。

例)Xcode.appをXcode8.appにリネーム

img1

↓

img2

新たにダウンロードしたバージョン7.3.1とバージョン8が共存

img3

次にコマンドツールを使う場合、パスを切り替える必要があります。

まずは現在のパスを確認

```sh
$ xcode-select -print-path
```

次に切り替えたいバージョンのXcodeのパスを設定します。

例) Xcode7.appにパスを通す場合

```sh
$ xcode-select --switch /Applications/Xcode7.app
```

うまく切り替わったか確認

```sh
$ xcrun --find xcodebuild
```
現在のアクティブなパスの`xcodebuild`を表示するはずです。


#### SearchPathsなどを設定する時によく使うマクロ
***

ヘッダーファイルのパスやらリンクさせるライブラリーのパスやらを<br>
設定する際によく使うマクロ

例) /Users/hoge/Develop/SampleApp/SampleApp.xcodeproj

|マクロ名|展開後|
|:------|:----|
|$(SRCROOT)|/Users/hoge/Develop/SampleApp|
|$(PROJECT_NAME)|SampleApp|
|$(FULL_PRODUCT_NAME)|SampleApp.app|
|$(CONFIGURATION)|Debug/Release|
