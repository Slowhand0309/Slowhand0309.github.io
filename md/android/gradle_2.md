## Gradleをちゃんと使って見る

今回は以前Eclipseで作成したAndroidの自作ライブラリを<br />
gradleでビルドできるようにしたいと思います。

#### Android用のテンプレートbuild.gradleを作成
***

Androidの[公式ガイドページ](http://tools.android.com/tech-docs/new-build-system/user-guide)に必要最低限の
Androidアプリをビルドする<br />
build.gradleが乗っていたのでそいつを使います。

```
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:1.3.1'
    }
}

apply plugin: 'android-library' # [1]

android {
    compileSdkVersion 23
    buildToolsVersion "23.1.0" #[2]
}
```

[1]で`com.android.application`となっていたものを今回ライブラリを作成するので<br />
android-libraryに修正しています。<br />
[2]に関しては自分の環境に合わせてください。

ここで、`AndroidSDK`の場所をどこかに設定しとかないといけません。<br />
環境変数`ANDROID_HOME`に設定するか、build.gradleと同じ階層に` local.properties`を作成し以下のように設定します

```
sdk.dir={AndroidSDKのパス}
```

この時点で`gradle tasks`を実行し成功すれば準備はOKです

#### gradle wrapperの作成
***

gradleには別の使う人がgradleを別途インストールしなくても,
自動で特定のgradleのバージョンをインストールし、配布した人と同じgradleのバージョンでビルドできるgradle wrapperという機能があります。

以下のタスクを実施
```
$ gradle wrapper
```

すると
```
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── gradlew
├── gradlew.bat
```
が追加されたと思います。配布された人は`gradlew.bat`(Windows用)。`gradlew`(その他OS用)<br />
を実行する事で`gradle`コマンドと同じように使えます

#### build.gradle作成例
***

プロジェクト毎に記述が異なると思いますが、↓の例は[こちら](https://github.com/Slowhand0309/Monaka)で公開している<br />
プロジェクトのbuild.gradleになります。

```
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:1.3.1'
    }
}

apply plugin: 'android-library'

repositories {
    flatDir {
        dirs 'libs'
    }
}

dependencies {
    compile 'com.android.support:support-v4:XX.X.X'
}

android {
    compileSdkVersion XX
    buildToolsVersion "XX.X.X"
    sourceSets {
        main {
            manifest.srcFile 'AndroidManifest.xml'
            java.srcDirs = ['src']
            resources.srcDirs = ['src']
            aidl.srcDirs = ['src']
            renderscript.srcDirs = ['src']
            res.srcDirs = ['res']
            assets.srcDirs = ['res']
        }
    }
}
```

特別な事はしてないですが、サポートライブラリの読み込みや`AndroidManifest.xml`の<br />
指定などを追加しています。

`gradle clean build`で
```
├── build
│   ├── outputs
│   │   ├── aar
│   │   │   ├── Monaka-debug.aar
│   │   │   └── Monaka-release.aar
```
が作成されていれば成功です。
