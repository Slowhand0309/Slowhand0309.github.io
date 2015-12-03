## iOSのUIWebView + AngularJS

iOSのアプリでメニュー画面なんか作る時は今までStoryboardから<br />
ボタン配置して〜とやっていたんですが、WebViewでやった方が色々<br />
便利そうだと思い、今回フロントのJSフレームワークの<br />
**AngularJS**でテストしてみました。

#### テスト用のプロジェクト作成
***

Storyboardで画面にUIWebViewを貼り付けます。

**img01**

UIWebViewのサイズクラスを調節し画面一杯にUIWebViewが表示するように設定します。

**img02**

UIWebViewからViewControllerへOutletとして接続します。

![optimized](http://slowhand0309.github.io/images/blog/webview_angularjs/03.gif)


表示する用のHTMLをプロジェクト内に作成します。

**img04**

HTMLを以下に編集。
```html
<!doctype html>
<html ng-app>
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.4/angular.min.js"></script>
  </head>
  <body>
    <div>
      <label>Name:</label>
      <input type="text" ng-model="yourName" placeholder="Enter a name here">
      <hr>
      <h1>Hello {{yourName}}!</h1>
    </div>
  </body>
</html>
```
今回は[AngularJSの公式サイト](https://angularjs.org)に載ってあるサンプルを使用。

ViewControllerを以下に修正。
```swift
import UIKit

class ViewController: UIViewController {

    @IBOutlet weak var webView: UIWebView!
    var url = NSBundle.mainBundle().pathForResource("index", ofType: "html");

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.

        let reqURL = NSURL(string: url!)
        let req = NSURLRequest(URL: reqURL!)
        webView.loadRequest(req)
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }

}
```
実行してみる。

![optimized](http://slowhand0309.github.io/images/blog/webview_angularjs/05.gif)

入力した内容が反映されてればOK

#### JSからのイベントをネイティブ側で受け取って見る
***

* まずはViewControllerにUIWebViewをDelegate

![optimized](http://slowhand0309.github.io/images/blog/webview_angularjs/06.gif)

* ViewControllerを以下に修正

```swift
import UIKit

class ViewController: UIViewController, UIWebViewDelegate {

    @IBOutlet weak var webView: UIWebView!
    var url = NSBundle.mainBundle().pathForResource("index", ofType: "html");

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.

        let reqURL = NSURL(string: url!)
        let req = NSURLRequest(URL: reqURL!)
        webView.loadRequest(req)
    }

    // handling event
    func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {

        println(request)
        println(navigationType)
        return true
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
}
```

`func webView`でWebViewが新たなフレームをロードする際にこのメソッドが実行されます。<br />
今回はそこでパラメータを出力しています。

* HTMLを以下に修正。

```html
<!doctype html>
<html ng-app="myApp">
    <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.4.4/angular.min.js"></script>
    </head>
    <body>
        <div>
            <label>Name:</label>
            <input type="text" ng-model="yourName" placeholder="Enter a name here">
            <hr>
            <h1>Hello {{yourName}}!</h1>
        </div>
        <div>
            <form ng-controller="myController">
                <lable>Link:</label>
                <button ng-click="onClick()">send</button>
            </form>
        </div>
    </body>
    <script>
        angular.module('myApp', [])
            .controller('myController', ['$scope', function($scope) {
                // setting click listener
                $scope.onClick = function() {
                    location.href = "http://www.yahoo.co.jp";
                };
            }]);
    </script>
</html>
```

* 実行中の画面

***img07***

* 「send」をクリックした際の出力結果

```
<NSMutableURLRequest: 0x7f8c9b713790> { URL: file:///Users/{ユーザ名}/Library/Developer/CoreSimulator/Devices/F1110364-4D6A-4383-9ED1-3BBF2798E8A5/data/Containers/Bundle/Application/595C8C08-B4DF-44C8-AF1C-72BF24AB3217/WebViewSample.app/index.html }
(Enum Value)
<NSMutableURLRequest: 0x7f8c9b42a590> { URL: http://www.yahoo.co.jp/ }
(Enum Value)
<NSMutableURLRequest: 0x7f8c9b588be0> { URL: http://m.yahoo.co.jp/ }
(Enum Value)
<NSMutableURLRequest: 0x7f8c9d9076e0> { URL: http://s.yimg.jp/images/listing/tool/yads/criteo_api.html#%7B%22command%22%3A%22callAds%22%2C%22params%22%3A%7B%22zoneid%22%3A%22265876%22%7D%7D }
(Enum Value)

```

リクエストの内容と`UIWebViewNavigationType`が`Enum Value`として表示されています。
<br />とりあえず今回はここまで。
