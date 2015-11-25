## Gradleをちゃんと使って見る

AndroidStudioでお世話になっている`Gradle`ですが<br />
一からちゃんと使った事がないので、ちゃんと使ってみます

そもそも・・・
> Gradleとは

Gradleは、Groovyで書かれたビルドシステム<br />
※Groovyは、Java VM上で動作する動的なスクリプト言語

とあります。

#### インストール
***

Gradleのインストール方法が以下の2パターンある模様

* [公式サイト](http://gradle.org)からバイナリをダウンロードしてインストール
* GVMを使用する

今回はGVM(Groovy enVironment Manager)を使って見る<br />
rubyのRVMみたいなもんで、複数バージョンのGradleを管理できる

以下コマンドでインストール
```
$ curl -s get.gvmtool.net | bash
```
現時点では`SDKMAN`というのに変更されているらしく、上のコマンドを実行すると<br />
`SDKMAN`がインストールされるが、`gvm`コマンドは`sdk`コマンドのエイリアスとして設定されます

```
$ which gvm
gvm: aliased to sdk
```

インストール可能な`Gradle`の一覧を表示
```
$ gvm list gradle
```

とりあえず現時点で最新の`2.9`をインストール
```
$ gvm install gradle 2.9
```

動作確認
```
$ gradle -version
```

#### とりあえず、Hello World
***

おきまりのHello WorldをGradleで書いてみたいと思います

`build.gradle`
```gradle
task helloworld << {
  println "Hello World!"
}
```

`gradle helloworld`で実行してみる
```
:helloworld
Hello World!

BUILD SUCCESSFUL
```
上のように成功になれば準備はOK

#### Javaクラスのコンパイル&実行
***

単純なJavaのクラスを作成
```java
package sample;

public class Sample {

  public static void main(String[] args) {
    System.out.println("print from Sample class");
  }
}
```

build.gradleを以下に編集
```
apply plugin: "java"

repositories {
  mavenCentral()
}
```

この時のフォルダ構成は以下
```
.
├── build.gradle
└── src
    └── main
        └── java
            └── sample
                └── Sample.java

```

早速コンパイルしてみる
```
$ gradle compileJava
:compileJava

BUILD SUCCESSFUL

Total time: 5.966 secs
```

ビルドに成功すると`build`ディレクトリが作成され、<br />
`build/classes/main/sample`にコンパイルされた`Sample.class`が作成されている
```
.
├── build
│   ├── classes
│   │   └── main
│   │       └── sample
│   │           └── Sample.class ★
│   ├── dependency-cache
│   └── tmp
│       └── compileJava
├── build.gradle
└── src
    └── main
        └── java
            └── sample
                └── Sample.java
```

実行してみる
```
$ java -cp build/classes/main sample.Sample
print from Sample class
```

上のように表示されれば成功

作成された`build`を消すには
```
$ gradle clean
```
で消えます
