## 新しいビルドツールBazelを試してみる

[Bazel](http://www.bazel.io)


> Bazelとは?

Google自信が使っていたビルドツールを公開したものです。<br>
Android、iOSなどでも使えるとのこと。現在はベータ版。

> 余談

なぜBazelを試そうかと思ったかというと、、<br>
最近やってる**Tensorflow**でBazelが使われていたからです。

#### 環境準備 & インストール
***

2016年6月現在サポートされているプラットフォームは

* Ubuntu Linux
* Mac OS X

です。

あと**JDK8以降**が必要とのこと。<br>
今回はVagrantでUbuntuの環境を用意してそこで試してみたいと思います。<br>
Ubuntuのバージョンが`Trusty (14.04 LTS) `であれば以下コマンドで<br>
JDK8をインストールします。

```sh
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java8-installer
```
ちなみに途中で`sudo apt-get update`を実行しないと、<br>
```sh
E: Unable to locate package oracle-java8-installer
```
のエラーが出るのでお忘れなく^^;

Ubuntuのバージョンが'Wily (15.10)'の場合は以下コマンドでインストール。
```sh
$ sudo apt-get install openjdk-8-jdk
```

正常にインストールできたか確認
```sh
$ java -version
java version "1.8.0_91"
Java(TM) SE Runtime Environment (build 1.8.0_91-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.91-b14, mixed mode)
```
良さそうです^^

あとは一気にBazelのインストールを行います。
```sh
$ echo "deb http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list
$ curl https://storage.googleapis.com/bazel-apt/doc/apt-key.pub.gpg | sudo apt-key add -
$ sudo apt-get update && sudo apt-get install bazel
$ sudo apt-get upgrade bazel
```

インストール確認
```sh
$ bazel version
Extracting Bazel installation...
Build label: 0.3.0
Build target: bazel-out/local-fastbuild/bin/src/main/java/com/google/devtools/build/lib/bazel/BazelServer_deploy.jar
Build time: Fri Jun 10 11:38:23 2016 (1465558703)
Build timestamp: 1465558703
Build timestamp as int: 1465558703
```

#### 簡単なJavaプロジェクト作成
***

試しに簡単なJavaのプロジェクトを作成し、ビルドしてみます。

* ディレクトリ構成

```
.
├── BUILD
├── WORKSPACE
└── src
    └── main
        └── java
            └── com
                └── slowhand
                    └── Sample.java
```

Bazelでは`workspace`毎の場所で作業します。<br>
workspaceにはルート直下に`WORKSPACE`ファイルが必要になります。<br>
`WORKSPACE`はライブラリの外部参照を記述したりしますが、<br>
今回は依存は無いので空のファイルを作成します。

* **Sample.java**

単純に`Hello bazel`と表示するだけのクラスです。

```java
package com.slowhand;

public class Sample {
  public static void main(String args[]) {
    System.out.println("Hello bazel");
  }
}
```

* **BUILD**

`BUILD`ファイルにはソースコードの配置や参照ライブラリなど記述します。

```
java_binary(
  name = "sample",
  srcs = glob(["**/*.java"]),
  main_class = "com.slowhand.Sample",
)
```

#### ビルド&実行
***

それではビルドしてみます。
```sh
$ bazel build //:sample
INFO: Found 1 target...
Target //:sample up-to-date:
  bazel-bin/sample.jar
  bazel-bin/sample
INFO: Elapsed time: 10.765s, Critical Path: 7.52s
```
ビルドが成功すると`bazel-bin`の配下に実行モジュールが作成されます。
```sh
$ bazel-bin/sample
Hello bazel
```
ちゃんと実行されてます^^

ちなみに、、`WORKSPACE`ファイルがないと以下のエラーになります。
```
The 'build' command is only supported from within a workspace.
```
