## XcodeのパッケージマネージャAlcatrazを使う


> Alcatrazとは

インストールする事でXcodeのプラグインやカラーテーマなどを管理するパッケージマネージャ


#### インストール
***

[公式ページ](http://alcatraz.io)を参考にインストール


```
$ curl -fsSL https://raw.githubusercontent.com/supermarin/Alcatraz/deploy/Scripts/install.sh | sh
```

インストールが完了し<br />
`Alcatraz successfully installed!!1!🍻   Please restart your Xcode.`<br />
が表示されればインストール成功。メッセージ通りXcodeを再起動

初回起動時にAlcatrazの読み込むか聞かれるので、`Load Bundle`を選択<br />
起動しメニュー > 「Window」内に`Package Manager`が表示されていればOK

img001

メニューの`Package Manager`を選択すると別ウィンドウが表示される

img002

#### カラーテーマのインストール
***

メニューの`Package Manager`を選択し`Color Themes`を選択

img003

今回は`Space Gray`をインストールしてみる

img004

インストール完了後は`REMOVE`に置き換わる

img005

Xcodeを再起動し、Preference > Fonts & Colors からSpace Grayを選択

img006

カラーテーマがSpace Grayに変更される

img007
