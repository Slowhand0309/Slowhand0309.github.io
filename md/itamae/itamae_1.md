## itamaeを使って見る1

> itamaeとは?
以前やった[chef](http://developabout0309.blogspot.jp/2015/05/2.html)を簡単に使えるようにしたもの

#### 環境準備
***

gemをインストール
```sh
$ mkdir itamae_test
$ cd itamae_test
$ bundle init
```

`Gemfile`にいかを追加
```ruby
gem "itamae"
```

#### ローカル環境でディレクトリ作成
***

単純にローカルで`mkdir hello`を行うだけのレシピを作成してみます。<br>
`itamae_test/hello.rb`を以下のないようで作成

```ruby
execute "mkhello" do
  command "mkdir hello"
end
```

早速実行してみる
```sh
$ bundle exec itamae local hello.rb -l debug
INFO : Starting Itamae...
DEBUG : Executing `mkdir -p /tmp/itamae_tmp`...
DEBUG :   exited with 0
DEBUG : Executing `chmod 777 /tmp/itamae_tmp`...
DEBUG :   exited with 0
INFO : Recipe: /Users/.../itamae_test/hello.rb
DEBUG :   execute[mkhello]
DEBUG :     execute[mkhello] action: run
DEBUG :       (in pre_action)
DEBUG :       (in set_current_attributes)
DEBUG :       (in show_differences)
INFO :       execute[mkhello] executed will change from 'false' to 'true'
DEBUG :       Executing `mkdir hello`...
DEBUG :         exited with 0
DEBUG :       This resource is updated.
DEBUG :       This resource is updated.
```

成功すれば`hello`ディレクトリが作成されているかと思います。

#### Vagrant環境でディレクトリ作成
***

上と同じ内容をVagrant上で実行してみます。<br>
実行対象のVagrantを起動。

```sh
$ vagrant up
```

Vagrant対象に対してコマンドを実行
```sh
$ bundle exec itamae ssh --vagrant hello.rb -l debug
INFO : Starting Itamae...
DEBUG : Executing `mkdir -p /tmp/itamae_tmp`...
DEBUG :   exited with 0
DEBUG : Executing `chmod 777 /tmp/itamae_tmp`...
DEBUG :   exited with 0
INFO : Recipe: /Users/.../itamae_test/hello.rb
DEBUG :   execute[mkhello]
DEBUG :     execute[mkhello] action: run
DEBUG :       (in pre_action)
DEBUG :       (in set_current_attributes)
DEBUG :       (in show_differences)
INFO :       execute[mkhello] executed will change from 'false' to 'true'
DEBUG :       Executing `mkdir hello`...
DEBUG :         exited with 0
DEBUG :       This resource is updated.
DEBUG :       This resource is updated.
```

vagnrat上にhelloディレクトリが作成されていれば成功

#### httpdのインストールと起動
***

もう少し実用的にVagrant上にhttpdをインストールしてサービスを起動する<br>
レシピを作成してみたいと思いまうす。

`httpd.rb`を作成

```ruby
package 'httpd' do
  action :install
end

service "httpd" do
  action [ :enable, :start ]
  name "httpd"
end
```

実行
```sh
$ bundle exec itamae ssh --vagrant httpd.rb -l debug
```

ちゃんと実行されているか確認
```sh
$ service httpd status
httpd (pid  XXXX) is running...
```

##### 軽く使ってみて・・・

シンプルに扱えるのがいいですね。<br>
Chef-soloしか使ってないという人は`itamae`お勧めです。
