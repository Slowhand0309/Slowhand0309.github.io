## PhoneGapを使用する 4 - SwiftでPlugin作成 Camera実装 -

[前回](http://developabout0309.blogspot.jp/2016/05/phonegap-3-swiftplugin.html)の続きをやっていきたいと思います。<br>
前回はプラグインを経由してコンソールに文字を出力するだけでしたが、<br>
今回はカメラの撮影画面を表示させてみたいと思います。

ちなみに現時点のプラグインのディレクトリ構成は、
```sh
├── custom-plugin-camera
│   ├── plugin.xml
│   ├── src
│   │   └── ios
│   │       └── CustomCamera.swift
│   └── www
│       └── CustomCamera.js
```
上のようになっています。

#### カメラ用のカスタムUIViewController作成
****

カメラ撮影用の画面を表示するカスタム`UIViewController`を作成します。<br>
名前は`CustomCameraViewController.swift`とし、
```sh
plugins/custom-plugin-camera
├── plugin.xml
├── src
│   └── ios
│       ├── CustomCamera.swift
│       └── CustomCameraViewController.swift ☆
└── www
    └── CustomCamera.js
```
`src/ios`配下に作成します。また`plugin.xml`に
```xml
<source-file src="src/ios/CustomCameraViewController.swift" />
```
を追加します。

次に`CustomCameraViewController.swift`を実装します。
```swift
import UIKit
import AVFoundation

class CustomCameraViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        // セッションの作成.
        let session = AVCaptureSession()

        // デバイス一覧の取得.
        let devices = AVCaptureDevice.devices()

        // バックカメラをmyDeviceに格納.
        var device : AVCaptureDevice!
        for d in devices{
            if(d.position == AVCaptureDevicePosition.Back){
                device = d as! AVCaptureDevice
            }
        }

        let videoInput: AVCaptureInput!
        // バックカメラからVideoInputを取得.
        do {
            videoInput = try AVCaptureDeviceInput.init(device: device)
        } catch {
            videoInput = nil
        }

        // セッションに追加.
        session.addInput(videoInput)

        // 出力先を生成.
        let imageOutput = AVCaptureStillImageOutput()

        // セッションに追加.
        session.addOutput(imageOutput)

        // 画像を表示するレイヤーを生成.
        let videoLayer : AVCaptureVideoPreviewLayer = AVCaptureVideoPreviewLayer.init(session: session)
        videoLayer.frame = self.view.bounds
        videoLayer.videoGravity = AVLayerVideoGravityResizeAspectFill

        // Viewに追加.
        self.view.layer.addSublayer(videoLayer)

        // セッション開始.
        session.startRunning()
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
    }
}
```
`UIViewController`クラスを継承したカスタムクラスを作成し、<br>
画面が表示されたタイミングで実機の背面カメラの撮影画面を表示しています。<br>

次に`CustomCamera.swift`から呼ぶようにします。
```swift
import Foundation

@objc(CustomCamera) class CustomCamera : CDVPlugin {

    func start(command: CDVInvokedUrlCommand) {        
        let v : CustomCameraViewController = CustomCameraViewController()
        self.viewController.addChildViewController(v)
        self.viewController.view.addSubview(v.view)
    }
}
```
`CDVPlugin`を継承しているので`self.viewController`経由で<br>
ViewControllerの追加、Viewの追加を行っています。

#### 検証
****

ここまで出来たら早速動かしてみたいと思いますが、、<br>
その前に・・プラグインを更新した場合、`phonegap cordova build`しても<br>
プラグインが反映されない場合があるので、
```sh
$ phonegap cordova platform rm ios
$ phonegap cordova platform add ios
```
上のように一度iosのプロジェクトを削除し、再度作成することでキレイに反映されます。<br>
※他にもしかしたらいい方法があるのかもしれませんが・・

今回はカメラを使うので、実機で確認します。<br>
`platforms/ios/XXXX.xcodeproj`を開き、Deviceに実機を選択し実行します。<br>
※ Xcode7以上でないと実機でデバッグはできません。

img1

また、実行時には設定が必要な場合があります。iphoneの場合、<br>
設定 > 一般 > プロファイルとデバイス管理でデベロッパAPPを選択し認証します。

img2

img3

実行して画面全体に背面カメラの撮影画面が表示されていれば成功です^^
