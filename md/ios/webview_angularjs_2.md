## iOSのUIWebView + AngularJSで画面遷移

[以前](http://developabout0309.blogspot.jp/2015/08/iosuiwebview-angularjs.html)にUIWebView+AngularJSで軽く動作させてみましたが、<br />
今回は画面遷移をやってみようと思います。

<br />
構成としては
* トップメニュー画面
* サブ画面
* メイン画面

で、トップメニュー画面とサブ画面がUIWebView上のHTMLで表示、<br />
メイン画面を通常のViewで実装してみたいと思います。

#### 独自URLスキームの作成
***

実装するアプリ専用のURLスキームを作成し、そのURLスキームで<br />
リクエストが来た場合のみ処理を行うようにしたいと思います。

<br />
> URLスキームとは

URLスキームは`http://hogehoge`のhttpを差します<br />
<br />
前回WebViewが新たなフレームをロードする時にハンドリングする<br />
メソッドをオーバーライドしました

```swift
func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {
      return true
    }
```

今回はここで`NSURLRequest`からURLを取得し、作成する独自スキームであれば
画面遷移を行うようにしたいと思います。

<br />
作成するURLスキームは`sample`として進めていきます<br />
`sample`をURLスキームとした例は以下のようになります
```HTML
  sample://hogehoge/fuga/index.html
```

#### 独自URLスキームのハンドリング
***

`func webView`を独自URLスキームを判定する為に以下のような内容で編集します

```swift
func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {

    let url = request.URL!.absoluteString
    if url.hasPrefix("sample://") {
        // 何かしらの処理
        return false
    }
    return true
}
```

`request.URL!.absoluteString`でリクエストURLのフルパスを取得しています<br />
次の行の`if url.hasPrefix("sample://")`で独自URLスキームかどうかの判定をしています<br />

#### 画面遷移(トップメニュー画面→サブ画面)
***

トップメニュー画面からサブ画面の画面遷移を実装してみたいと思います。<br />
別のHTMLをロードしてほしいリクエストURLを`sample://html/XXXX`とします<br />
XXXにはロードしたいhtml名を設定します。

例)
  menu.htmlからsub.htmlを表示させたい時<br />
  `sample://html/sub`

URLはサンプル用です<br />
html名を与えたらhtmlをロードしてくれるメソッドを作成します

```swift
private func locationChanged(res :String) {
    let url = NSBundle.mainBundle().pathForResource(res, ofType: "html");
    let reqURL = NSURL(string: url!)
    let req = NSURLRequest(URL: reqURL!)
    webView.loadRequest(req)
}
```
↑では引数の`res`の名前+拡張子が`html`のリソースパスを取得し、<br />
リソースパスをリクエストURLに変換しUIWebViewにロードさせています

作成したメソッドを使って`func webView`を以下のように修正します

```swift
func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {

    let url = request.URL!.absoluteString
    if url.hasPrefix("sample://") {
        if url == "sample://html/sub" {
          locationChanged("sub")
        }
        return false
    }
    return true
}
```

リクエストURLが`sample://html/sub`の場合`locationChanged`に<br />
引数"sub"を渡して呼び出しています

* menu.html

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
                    location.href = "sample://html/sub";
                };
            }]);
    </script>
</html>
```

前回作成したindex.htmlでのボタン押したリンク先をサブ画面に変更しています


* sub.html

```html
<!doctype html>
<html>
    <head>
        <title>Sub Screen</title>
    </head>
    <body>
        <h2>Sub Screen</h2>
    </body>
</html>
```

サブ画面はいたって単純でSub Screenと表示するだけです

↓画面遷移の様子

![optimaize](http://slowhand0309.github.io/images/blog/webview_angularjs/10.gif)

#### 画面遷移(トップメニュー画面→メイン画面)
***

トップメニュー画面からメイン画面への遷移ですが、
<br />こちらも特定のリクエストURLの場合にハンドリングするのは変わらず、<br />
ハンドリングしたら別UIViewControllerへ遷移させます

別UIViewControllerへ遷移する為のメソッドを作成します

```swift
// Screen transition MainViewController.
private func startMainView() {
    let mainViewController = self.storyboard!
        .instantiateViewControllerWithIdentifier("mainview")
    self.presentViewController(mainViewController, animated: false, completion: nil)
}
```
↑ではストーリーボード上の遷移先のStoryboard ID`mainview`を取得し<br />
`presentViewController`で画面遷移させています

> Storyboard IDのつけ方

![storyboardid](http://slowhand0309.github.io/images/blog/webview_angularjs/11.gif)

今度は`sample://html/sub`のリクエストURLが来たら<br />
`startMainView`を呼ぶように`func webView`を変更します

```swift
// handling event
func webView(webView: UIWebView, shouldStartLoadWithRequest request: NSURLRequest, navigationType: UIWebViewNavigationType) -> Bool {

    let url = request.URL!.absoluteString
    if url.hasPrefix("sample://") {
        if url == "sample://html/sub" {
            locationChanged("sub")
        } else if url == "sample://html/main" {
            startMainView()
        }
        return false
    }
    return true
}
```

また、menu.htmlもメイン画面へのリンクボタンを追加します
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
                <lable>Link: Sub</label>
                <button ng-click="onClickSub()">send</button>
                <lable>Link: MainView</label>
                <button ng-click="onClickMain()">go</button>
            </form>
        </div>
    </body>
    <script>
        angular.module('myApp', [])
            .controller('myController', ['$scope', function($scope) {
                // setting click listener
                $scope.onClickSub = function() {
                    location.href = "sample://html/sub";
                };

                // setting click listener
                $scope.onClickMain = function() {
                    location.href = "sample://html/main";
                };
            }]);
    </script>
</html>
```

↓実行結果

![optimaize](http://slowhand0309.github.io/images/blog/webview_angularjs/12.gif)
