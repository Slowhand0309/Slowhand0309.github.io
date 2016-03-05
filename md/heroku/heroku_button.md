## Heroku Buttonを設置してみる

Paas環境のサービス**Heroku**には[Heroku Button](https://devcenter.heroku.com/articles/heroku-button)があります

ボタン一つでアプリケーションを自分のHeroku環境にデプロイできてしまう<br>便利なボタンです。

今回は[MichiEki](https://github.com/Slowhand0309/MichiEki)のHeroku Buttonを作成してみたいと思います<br>
※ MichiEkiは道の駅好きな筆者が勢い余って作ったAPIです^^;


#### とりあえずできたものを動かしてみる
***

作成の手順の前に・・まずは作成したボタンを以下に貼ってます。<br>
このボタンを押すと自分のHeroku環境にデプロイできます。

[![Heroku Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy?template=https://github.com/Slowhand0309/MichiEki)

押すと、こんな画面になるので

img1

そのまま`Deploy to Free`を押すとデプロイが開始されます。

img2

デプロイが完了すると`View`ボタンが表示されるのでそのまま押して<br>
以下の画面が表示されれば成功です。

img3

#### Heroku Button作成
***

作成はとっても簡単です。<br>
自分のGithubプロジェクトの直下に`app.json`を作成し、ボタンのリンクを貼るだけです。とは言えプロジェクトはデプロイできる形式にしとかないといけません。

* app.json

```json
{
  "name": "MichiEki",
  "description": "API for provides information Road Station",
  "success_url": "/api/info",
  "env": {
    "BUILDPACK_URL": "https://github.com/heroku/heroku-buildpack-ruby"
  }
}
```

今回は`name`と`description`、`success_url`、`env`を指定しています。

* name ・・・ アプリ名
* description ・・・アプリの詳細
* success_url ・・・ デプロイ成功時に叩かれるURL
* env ・・・ 今回は`buildpack`を指定しています

`buildpack`は実行環境をまるっとパックしたもので、デプロイするアプリが動作するのに必要な環境をurlで指定します。<br>
今回はGithubで公開されているHerokuのruby環境を指定しています。<br>
自分で環境を作成しGithubに上げたurlを指定することも可能です

その他のスキーマに関しては[こちら](https://devcenter.heroku.com/articles/app-json-schema)を参照

次にHeroku Buttonのリンクを貼ります。<br>
Github上の`README.md`に貼る場合は

```
[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://heroku.com/deploy)
```

その他のサイトの場合は(上で貼ったリンクも↓になります)

```html
<!-- template=[デプロイさせたいGithubのレポジトリのURL] -->
<a href="https://heroku.com/deploy?template=https://github.com/Slowhand0309/MichiEki">
<img src="https://www.herokucdn.com/deploy/button.png" alt="Heroku Deploy">
</a>
```

になります。

いや〜便利ですw
