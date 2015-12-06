## CocoaPodsを使って見る


> CocoaPodsとは

OSXのアプリやiOSアプリでのライブラリ管理ツール

#### インストール
****

gemで提供されているので、rvmで専用のgemsetを作成する

```sh
$ rvm gemset create pod
```

CocoaPodsインストール
```sh
$ gem install cocoapods

$ which pod
/Users/XXXXXXX/.rvm/gems/ruby-2.1.0@pod/bin/pod
```

セットアップ

```sh
$ pod setup
Setting up CocoaPods master repo
Setup completed
```

とりあえずセットアップまではこれで完了

#### 実際に使う
****

使いたいプロジェクトへ移動 <br />
※今回はiOSアプリ開発プロジェクトとして新規作成した`TestSwift`プロジェクトを使用
```sh
$ cd TestSwift
```

`Podfile`をプロジェクト直下に作成し、以下内容に編集
```ruby
platform :ios, "6.0"
pod 'AFNetworking', '~> 2.0'
```
↑例として`AFNetworking`ライブラリをiOS開発プロジェクトに取り込む

ライブラリ取り込む前のファイル一覧
```sh
$ ls
Podfile             TestSwift           TestSwift.xcodeproj TestSwiftTests
```

ライブラリ取り込み
```sh
$ pod install
Updating local specs repositories
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.5.4)
Generating Pods project
Integrating client project

[!] Please close any current Xcode sessions and use `TestSwift.xcworkspace` for this project from now on.
Sending stats
Pod installation complete! There is 1 dependency from the Podfile and 1 total pod
installed.
```

↓インストール後のファイル
```sh
$ ls
Podfile               Pods                  TestSwift.xcodeproj   TestSwiftTests
Podfile.lock          TestSwift             TestSwift.xcworkspace
```
作成された`TestSwift.xcworkspace`を開いて開発を行う

* Xcodeを開いた様子

また、今回はSwiftからObjective-Cのライブラリを使用する為、
ブリッジ用のヘッダを作成する

```C
#ifndef _AFTest_Bridging_Header_h
#define _AFTest_Bridging_Header_h

#import <AFNetworking/AFNetworking.h>

#endif /* _AFTest_Bridging_Header_h */
```
これをプロジェクトに追加し`Swift Compiler - Code Generation`の`Objective-C Bridging Header`に設定してやる

これで`AFNetworking`ライブラリが使用可能になればOK
