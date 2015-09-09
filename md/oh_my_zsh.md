Oh My Zsh
===

ちょっと前から、zshをいい感じに使いやすくしてくれる**Oh My Zsh**を導入<br />
色々使ってみた感想などなどをまとめてみた。

> そもそも・・Oh My Zshとは？

zshを拡張して管理してくれるフレームワーク。オープンソース<br />
テーマが沢山あって外見を好きにカスタマイズできる。また便利なプラグインが豊富。

### インストール方法

`curl`や`wget`の以下コマンドでインストール可能

* curl
```
curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh
```

* wget
```
wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -
```

↓インストール直後<br />
img01

### 設定の編集

`~/.zshrc`の設定ファイルを編集して、テーマやプラグインをカスタマイズする。

* テーマの設定

`ZSH_THEME`の設定を変えてやる事でテーマを変更できる。<br />
テーマの一覧は[ここ](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)を参照。
デフォルトは`ZSH_THEME="robbyrussell"`

例)`ZSH_THEME="avit"`

img02

例)`ZSH_THEME="smt"`

img03

現在インストールされているテーマは`~/.oh-my-zsh/themes`配下で確認できる。

* プラグインの設定

使いたいものを`plugins=()`で設定する。
かっこの中に使いたいプラグインを複数設定できる。

例) ↓現在個人的に使っているプラグインの設定<br />
`plugins=(git ruby osx bundler brew rails emoji-clock atom)`

以下に主なプラグインの説明を載っけてます。

> git

gitに関する便利なaliasを提供。

| Alias | Command |
|:------|:--------|
|g |git|
|ga|git add|
|gaa|git add --all|
|gapa|git add --patch|

・・・などなど、[ここ](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git)を参照。

> ruby

以下aliasを提供。

| Alias | Command |
|:------|:--------|
|rfind|find . -name "*.rb" &#124; xargs grep -n|
|sgem|sudo gem|

> osx

ターミナルで現在のディレクトリを新規タブで開いたり、<br />
Finderで現在開いているディレクトリに移動したりなどなど。


> atom

結構このatomのプラグインが個人的には**超便利**で使ってます。

| Alias | 説明 |
|:------|:--------|
|at|atomを起動|
|at $folder|指定したフォルダに移動して、その場所でatomを起動します|
|at $file|指定したファイルをatomで開きます|
|att|現在のディレクトリでatomを起動します|

現在インストールされているプラグインは`~/.oh-my-zsh/plugins`配下で確認できる。


他にも色々あるので、詳しくは[ここ](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)を参照して下さい。

![optimized](http://slowhand0309.github.io/images/blog/oh-my-zsh/oh-my-zsh-ctrl+r.gif)
