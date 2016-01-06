## Rackアプリケーションのテスト

新年一発目からマニアックです^^;<br>

このブログにも散々投稿してる道の駅巡りですが、好きが生じて<br>
道の駅の情報(位置など)を取得できるAPIを作成しました。

Githubに[MichiEki](https://github.com/Slowhand0309/MichiEki)として公開してます。

現在は道の駅の検索くらいしかできませんが、今後色々機能を<br>
追加していきたい所です。

#### 本題
***

こっからが本題ですが^^;、このMichiEkiはrackアプリケーションとなっており<br>
`rackup`コマンドで起動します。起動したMichiEkiアプリに対して色々な<br>
リクエストを投げるとJSON形式でデータが返ってきます。

今回はこのRackアプリケーションのテストを書いていこうと思います。<br>
テストは`RSpec`を使ってテストします。

* 環境準備

`bundle init`を行い、できた`Gemfile`に以下gemを追加します

```ruby
gem 'rake'
gem 'rspec'
gem 'sham_rack'
```

↑で`sham_rack`というgemをインストールしてますが、<br>
このgemがスタブやモックの働きをしてくれます。

インストールと初期化

```sh
$ bundle install
$ rspec --init
```

ここまでで以下のようなツリー構成になっているかと思います
```
.
├── Gemfile
├── Gemfile.lock
└── spec
    └── spec_helper.rb
```

サンプルとして`spec/hello.rb`を作成します

```ruby
# coding: utf-8

class HelloApp
  def call(env)
    case env['REQUEST_METHOD']
    when 'GET'
      [   
        200,
        { 'Content-Type' => 'text/html' },
        ['<html><body><p>hello</p></body></html>']
      ]   
    end
  end
end
```

単純にリクエストが来たら`hello`を返しているだけのものです。<br>
次に`config.ru`を作成します

```ruby
# coding: utf-8

require_relative 'hello'

run HelloApp.new
```

ここまで作成できたら、一度試しに実行確認。
```sh
$ bundle exec rackup -o 0.0.0.0
```

`http://localhost:9292/`にアクセスして`hello`が表示されればOK

#### sham_rackを使ったテスト
***

Rackアプリケーションをテストする際に便利な`sham_rack`というのがあります<br>

> sham_rackとは?

HTTPリクエストをホストに投げる前にフックすることができ、<br>
モックやテスト用のドメインとしても使うことができます。

実際に`sham_rack`を使ってテストしていきます。<br>
`spec_helper.rb`に以下を追加します。

```ruby
require 'sham_rack'
require_relative '../hello'

RSpec.configure do |config|
  ・・・
end

ShamRack.at('sham.rack.example.com').rackup do
  run HelloApp.new
end
```

これで`sham.rack.example.com`に対してリクエストを送ると<br>
フックされHelloAppが呼び出されます。

hello.rbのテスト`hello_spec.rb`を以下内容で作成します

```ruby
require "spec_helper"
require 'open-uri'

RSpec.describe HelloApp do

  it 'test hello' do
    result = open("http://sham.rack.example.com").read
    p result
    expect(result).to eq '<html><body><p>hello</p></body></html>'
  end
end
```

`http://sham.rack.example.com`にリクエストを送ると<br>
`<html><body><p>hello</p></body></html>`が返ってくることを期待します

実行してみます。

```sh
$ bundle exec rspec
Run options: include {:focus=>true}

All examples were filtered out; ignoring {:focus=>true}
"<html><body><p>hello</p></body></html>"
.

Finished in 0.02474 seconds (files took 0.17352 seconds to load)
1 example, 0 failures
```

ちゃんとリクエストがフックされ`HelloApp#call`が呼ばれ、<br>
`<html><body><p>hello</p></body></html>`が返ってきてます。
