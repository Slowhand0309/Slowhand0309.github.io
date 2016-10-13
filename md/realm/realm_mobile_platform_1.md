## Realm mobile platform アプリ作成 - iOS -

[以前](http://developabout0309.blogspot.jp/2016/10/7-realm-mobile-platform.html)デモアプリを試して、サーバーと同期が取れている事を試してみました。
今回は実際にiOSアプリに組み込んでみたいと思います。

##### プロジェクト作成
****

今回はXcode8でプロジェクトを作成していきます。

`Create a new Xcode project` > `iOS` > <br>
`Application` > `Single View Application`を選択。

`Product Name`や`Organization Name`等はお好みで。<br>
Languegeは`Swift`、Devicesは`iPhone`を選択します。

img1

Keychainを使用する為、`Keychain Sharing`をONにします。<br>
※「Capabilities」タブにあります

img2

次に「General」タブの`Embedded Binaries`に前回ダウンロードした<br>
`realm-mobile-platform`フォルダ内の「SDKs」>「realm_cocoa_XXX」> <br>
「ios」>「swift-3.0」にある「Realm.framework」と「RealmSwift.framework」を<br>
ドラッグ&ドロップします。

img3

その際に「Copy Items if Needed」にチェックを入れます。

img4

コピー後<br>
img5

##### モデルの作成
****

次に簡単なモデルを作成します。<br>
`ViewController.swift`に以下を追加します。

```swift
import RealmSwift

final class Item : Object {
    dynamic var id = 0
    dynamic var name = ""
}
```

モデルの作成は`Object`クラスを継承したクラスを作成し、<br>
メンバは`dynamic var`で宣言します。


##### ユーザーログイン
****

アプリ起動時にサーバーにログインする処理を追加します。

```swift
var realm: Realm!

func login() {
    // サーバーURLを指定
    let url = URL(string: "http://localhost:9080")
    // 登録済みのユーザーで認証します
    let credential = Credential.usernamePassword(
      username: "user@example.com", password: "xxxxxx", actions: [.useExistingAccount])
    User.authenticate(with: credential, server: url!, onCompletion: {user, error in
        if let _ = user {
            // ログイン成功時
            // sample/database : DBで言うスキーマみたいなもの
            let realmURL = URL(string: "realm://localhost:9080/sample/database")!
            let configuration = Realm.Configuration(syncConfiguration: (user!, realmURL))
            Realm.Configuration.defaultConfiguration = configuration
            // realmオブジェクトを取得
            self.realm = try! Realm()

        } else if let _ = error {
            // ログイン失敗
            print("login error \(error)")
        }
    })
}

override func viewDidLoad() {
    super.viewDidLoad()
    login()
}
```

ログイン成功時に取得した`Realm`オブジェクトを使って処理を行います。

##### アイテム追加
****

ストーリーボードで`Button`と`TextField`を追加します。

img6

Button押下時のアクションを`onPushAdd`として`ViewController.swift`に<br>
イベント追加します。また、`TextField`も`nameField`として追加します。

img7

img8

追加した`onPushAdd`を以下のように修正します。

```swift
var count: Int = 0

@IBAction func onPushAdd(_ sender: AnyObject) {
    // TextFieldから現在入力されているテキストを取得
    let name = self.nameField.text
    try! self.realm?.write {
        // RealmオブジェクトにてItemを追加
        count = count + 1
        let item = Item()
        item.id = count
        item.name = name!
        self.realm.add(item)
    }
}
```

ここまで出来たらサーバー側で反映されているか確認します。<br>
`Realm Object Server`を起動し、`Realm Browser.app`を立ち上げます。

サーバー接続後、Openできるスキーマが増えているかと思います。

img9

ログイン時に設定したスキーマになります。<br>
※スキーマという名前であってるか分かりませんが、便宜上そう読んでます。

接続後に起動したアプリ上で`TextField`に文字列を入力し`Add`を押すと<br>
データが追加されてればOKです。

gif1

今回修正した`ViewController.swift`は[ここ](https://gist.github.com/Slowhand0309/ee1760b10c74250e9cd9619af8f62ba7)で見れます。
