## SwiftでRSpecみたいに書けるQuickを使ってみる 1

> Quickとは?

SwiftやObjective-Cで`RSpec`みたいに書けるテストフレームワーク<br>
[公式ページ](https://github.com/Quick/Quick)

また、Quickを使うには`Nimble`が必要らしい。

> Nimbleって?

ざっくりな説明ですが・・・<br>
[以前](http://developabout0309.blogspot.jp/2015/07/swiftxcttest.html)にXcodeに含まれる標準のXCTestを軽く紹介しましたが、<br>
このXCTestで例えば `1 + 1 = 2`のテストを書くと、

```swift
XCTAssertEqual(1 + 1, 2, "1足す1は2!!")
```

とこうなるが、`Nimble`では

```swift
expect(1 + 1).to(equal(2))
```

こう書くことができる。直感的です^^

#### インストール
****

早速インストール・・の前に今回テスト用のプロジェクトを作成します。<br>
テンプレートとして`Single View Application`を選択。

img1

プロジェクト名を`QuickSample`としました。

img2

※ Testにチェックを忘れずに入れる

そしてインストールは`CocoaPods`を使ってインストールしていきます。<br>
※ `CocoaPods`に関しては[こちら](http://developabout0309.blogspot.jp/2015/12/cocoapods.html)を参照

作成したプロジェクト直下に`Podfile`を以下の内容で作成。

```
use_frameworks!

def quick_pods
    pod 'Quick', '~> 0.9.0'
    pod 'Nimble', '3.0.0'
end

target 'QuickSampleTests' do
    quick_pods
end
```

インストールを実行

```sh
$ pod install
```

インストールが成功すると`QuickSample.xcworkspace`が作成されているので、<br>
こちらを開きます。

```sh
$ open QuickSample.xcworkspace
```

img3

これでインストールは完了です。

### テストを書いてみる
****

実際にテストを書いてみたいと思います。<br>
プロジェクト内に`QuickSampleTests`が作成されていると思うので、<br>
まず名前を`QuickSampleSpec`に変更します。<br>
次に`QuickSampleSpec`を以下に編集します。

```swift
import Quick
import Nimble

class QuickSampleSpec: QuickSpec {
    override func spec() {
        describe("sample test") {
            it("1 + 1 = 2!!") {
                expect(1 + 1).to(equal(2))
            }
        }
    }
}
```

いたってシンプルですが、`RSpec`のような構文になっているかと思います。<br>
当然テスト結果は成功になります。

img4

また、配列に含まれているかのテストも↓のように書けます。

```swift
it("array contain") {
    expect(["luffy", "chopper", "zoro"]).to(contain("zoro"))
}
```

他にも便利なものがたくさんありますが、今回はここまで。
