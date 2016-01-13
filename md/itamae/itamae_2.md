## itamaeを使って見る2

前回`itamae`を使ってvagrant上のVMにhttpdをインストール&起動しましたが、<br>
今回は`serverspec`を使って実際にちゃんと動作しているのかテストしたいと思います。

> serverspecとは?

サーバーの状態をテストするフレームワーク。RSpecの書き方に準拠。

#### serverspecのインストール
***

前回の`Gemfile`に以下を追加

```ruby
gem "rake"
gem "serverspec"
```
インストールし初期設定を行う
```sh
$ bundle install --path vendor/bundle
$ bundle exec serverspec-init
Select OS type:

  1) UN*X
  2) Windows

Select number: 1

Select a backend type:

  1) SSH
  2) Exec (local)

Select number: 1

Vagrant instance y/n: y
Auto-configure Vagrant from Vagrantfile? y/n: y
 + spec/
 + spec/default/
 + spec/default/sample_spec.rb
 + spec/spec_helper.rb
 + Rakefile
 + .rspec
```

#### スペックの作成
***

serverspecの初期化を行うと`spec/default/sample_spec.rb`が作成されています<br>
その中を見るとすでに`httpd`のテストが書かれています。これをそのまま使ってみます。`httpd_spec.rb`としてコピーし不要なものを削除。

```ruby
require 'spec_helper'

describe package('httpd'), :if => os[:family] == 'redhat' do
  it { should be_installed }
end

describe service('httpd'), :if => os[:family] == 'redhat' do
  it { should be_enabled }
  it { should be_running }
end

describe port(80) do
  it { should be_listening }
end
```

実行してみます。
```sh
$ bundle exec rake spec
Package "httpd"
  should be installed

Service "httpd"
  should be enabled
  should be running

Port "80"
  should be listening

Package "httpd"
  should be installed

Service "httpd"
  should be enabled
  should be running

Port "80"
  should be listening

Finished in 0.26789 seconds (files took 6.26 seconds to load)
8 examples, 0 failures
```

上のように`0 failures`になればOK
