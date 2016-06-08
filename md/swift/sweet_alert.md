## オシャレなアラート Sweet Alert iOS

今回は味気ない標準のアラートをシャレたアラートにしてくれる<br>
[Sweet Alert iOS](https://github.com/codestergit/SweetAlert-iOS)を使ってみます。

###### 標準のアラート
***

まずは標準のアラートです。

![sweet_alert_1](http://slowhand0309.github.io/images/blog/sweet_alert/sweet_alert_1.gif)

はい、いたってシンプルです。

###### メッセージのみ
***

では`Sweet Alert iOS`を使ってアラートを表示させてみます。<br>
まずはシンプルなメッセージのみのアラート

```swift
SweetAlert().showAlert("message")
```

![sweet_alert_2](http://slowhand0309.github.io/images/blog/sweet_alert/sweet_alert_2.gif)

アニメーションが付いてリッチな感じです。

###### タイトル + Success
***

次はアラートにタイトルを付け、Successのアラートスタイルで表示させてみます。<br>

```swift
SweetAlert().showAlert("message", subTitle: "subTitle", style: AlertStyle.Success)
```

![sweet_alert_3](http://slowhand0309.github.io/images/blog/sweet_alert/sweet_alert_3.gif)

アラートスタイルは`enum`で定義されており、

* Success
* Error
* Warning
* None
* CustomImag(imageFile:String)

の5種類が設定できます。

###### ボタンイベント
***

最後にアラートにカスタムボタンを表示されてみます。<br>

```swift
SweetAlert().showAlert("message", subTitle: "subTitle", style: AlertStyle.Warning, buttonTitle:"Cancel", buttonColor:UIColor.redColor() , otherButtonTitle: "OK", otherButtonColor: UIColor.blueColor()) { (isOtherButton) -> Void in
    if isOtherButton == true {

        print("Cancel Button Pressed")
    }
    else {
        SweetAlert().showAlert("OK", subTitle: "subTitle", style: AlertStyle.Success)
    }
}
```

![sweet_alert_4](http://slowhand0309.github.io/images/blog/sweet_alert/sweet_alert_4.gif)

OKボタンが押されると別のアラートを表示させています。
