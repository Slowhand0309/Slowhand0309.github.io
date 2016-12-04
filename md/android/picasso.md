## Android画像ライブラリ-Picasso-を試す

今回は[Picasso](http://square.github.io/picasso/)を試してみたいと思います。<br>
画像のダウンロード先はDocker+Rackで画像サーバーを作成(ただやりたいだけ)

> Picassoとは?

Androidで画像のダウンロードやキャッシュをいい感じにやってくれるライブラリ

#### 画像サーバー
****

わざわざDocker+Rackで作成しなくてもいいとは思いますが、、:sweat_drops:<br>
試しにやってみました。

* Dockerfile

  ```
  FROM ruby:2.3.0
  ENV LANG C.UTF-8
  ENV HOME_DIR /usr/src/app
  WORKDIR $HOME_DIR

  RUN apt-get update -qq

  COPY Gemfile $HOME_DIR/

  RUN bundle install
  ADD . $HOME_DIR
  ```

* Gemfile

  ```
  source 'https://rubygems.org'

  gem 'rack'
  ```

`docker-cpmpose`も使ってみます。

* docker-cpmpose.yml

  ```
  version: '2'
  services:
    web:
      build: .
      command: rackup -p 9292 -o 0.0.0.0
      environment:
        RAILS_ENV: development
      ports:
        - '9292:9292'
      volumes:
        - .:/usr/src/app
  ```

* config.ru

  ```
  # encoding: utf-8
  run Rack::Directory.new(File.dirname(__FILE__) + "/public")
  ```

  public配下を`http://localhost:9292/`でアクセスできるように公開してます。

画像はDockerfileと同じディレクトリ内に以下パスで画像を置きました<br>
`public/images/sample.jpg`

早速実行:sparkles:

```sh
$ docker-cpmpose up -d
```

ブラウザで`http://localhost:9292/images/sample.jpg`にアクセスし画像が表示されたら成功です。

停止は`docker-compose stop`

#### Androidアプリ作成
****

ようやくのアプリ作成です:droplet:

まずはプロジェクトの`build.gradle`に以下を追加
```
android {
    ・・・
    dataBinding {
        enabled = true
    }
}

dependencies {
    ・・・
    compile 'com.squareup.picasso:picasso:2.5.2'
}
```
ついでにdatabindingも有効にしときます。

次にレイアウトファイルの修正<br>
```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android" >

    <RelativeLayout
      ・・・ >

        <Button
            android:id="@+id/button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="Button"
            android:layout_alignParentBottom="true"
            android:layout_alignParentEnd="true" />

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentStart="true"
            android:id="@+id/imageView"
            android:layout_above="@+id/button"
            android:layout_alignParentEnd="true"
            android:layout_alignParentTop="true" />
    </RelativeLayout>
</layout>
```

そしてActivity内でボタンが押されたら「画像をダウンロードしてImageViewに設定する」<br>
という処理を実装して行きます。

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mBinding = DataBindingUtil.setContentView(this, R.layout.activity_main);
    Button button = mBinding.button;

    button.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View v) {
          ImageView imageView = mBinding.imageView;
          Picasso.with(MainActivity.this)
                 .load("http://xxxx/images/sample.jpg")
                 .into(imageView);
        }
    });
}
```

↓実際にPicassoを使っている所ですがメソッドチェーンで分かり易いです:sparkles:
```java
Picasso.with(MainActivity.this)
       .load("http://xxxx/images/sample.jpg")
       .into(imageView);
```

※ 自分の環境では`Docker for Mac` + `Android Emulator`で動作させようとしたらEmulatorが動作しなかったので<br>
以前使った[ngrok](http://developabout0309.blogspot.jp/2016/09/ngrok-localhost.html)を使ってlocalhostを外部公開して使ってます。

* 動作

![gif](http://slowhand0309.github.io/images/blog/android/picasso/picasso.gif)

画像は宮島の鹿ですw

Picassoではその他`リサイズ`や`回転`等色々できるようです。
```java
Picasso.with(MainActivity.this)
       .load("http://xxxx/images/sample.jpg")
       .resize(50, 50) // リサイズ
       .rotate(90.0f)  // 回転
       .error(R.drawable.hoge) // エラー時の画像
       .into(imageView);
```
