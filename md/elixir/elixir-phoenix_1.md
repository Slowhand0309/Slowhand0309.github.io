## 最近よく聞くElixir/Phoenixを使ってみる

* [Elixir](http://elixir-lang.org/)
* [Phoenix](http://www.phoenixframework.org)

> Elixirとは

Erland VM上で動作するRubyに似た関数型言語

> Phoenixとは

Elixirで実装されたウェブアプリケーションフレームワーク<br>
Railsによく似ている(作者がRailsコミッタでもある)

#### 環境作成
***
環境はVagrant(CentOS)+chef-soloで作成したいと思います。<br>
Vagrantの環境作成は[こちら](http://developabout0309.blogspot.jp/2015/05/vagrant.html), Chefの環境作成は[こちら](http://developabout0309.blogspot.jp/2015/05/2.html)を参照

- まずは専用のVagrantを作成<br>

```
$ vagrant box list
centos6  (virtualbox, 0)
$ vagrant init centos6
```
Vagrantfileを以下に編集
1. ↓コメントアウト + port:4000をforwardするように修正<br>
`config.vm.network "forwarded_port", guest: 4000, host: 4000`
2. ↓コメントアウト<br>
`config.vm.network "private_network", ip: "192.168.33.10"`

vagrant起動
```
$ vagrant up
```
<br>
- ssh設定

ssh.configにIPとホスト名の紐付けを行う
```
$ vagrant ssh-config --host elixir-phoenix >> ~/.ssh/config
```
ホスト名は`elixir-phoenix`

<br>
- サーバーにchef-soloインストール

```
$ knife solo prepare elixir-phoenix
```
<br>

#### Cookbook作成
***

- Elixirインストール用Cookbookの作成

```
$ knife cookbook create elixir -o site-cookbooks
```

各ファイルを以下内容で作成

*Berksfile*
```ruby
source "https://supermarket.chef.io"
cookbook 'elixir', path: 'site-cookbooks/elixir'
```

*metadata.rb*
```ruby
name             'elixir'
maintainer       '{name}'
maintainer_email '{name}@gmail.com'
license          'All rights reserved'
description      'Installs/Configures elixir'
long_description IO.read(File.join(File.dirname(__FILE__), 'README.md'))
version          '0.1.0'

depends "git"
depends "github"
depends "erlang"
```

*attributes/default.rb*
```ruby
default['elixir']['version'] = '1.0.5'
default['elixir']['home'] = '/usr/local/elixir'
default['elixir']['link'] = '/usr/local/elixir/bin'
default['elixir']['repo'] = "https://github.com/elixir-lang/elixir.git"
default['elixir']['source']['path'] = '/usr/local/elixir/src'
```

*recipes/default.rb*
```ruby
# install erlang
node.set[:erlang][:install_method]    = "source"
node.set[:erlang][:source][:version]  = "18.0"
node.set[:erlang][:source][:url]      = "http://erlang.org/download/otp_src_#{node[:erlang][:source][:version]}.tar.gz"
node.set[:erlang][:source][:checksum] = "a0b69da34b4f218eb7d63d9e96fc120aa7257bb6c37a0f40fb388e188b4111aa"

include_recipe "erlang::default"
include_recipe "git::default"

# create directory
directory node[:elixir][:home] do
  owner 'vagrant'
  group 'vagrant'
  mode '0755'
  action :create
end

# git clone to specified dir
git "elixir" do
  repository node[:elixir][:repo]
  revision "v#{node[:elixir][:version]}"
  destination node[:elixir][:source][:path]
  action :sync
end

# make
bash "make" do
  cwd node[:elixir][:source][:path]
  code "make"
  action :run
end

# create symbliclink
link node[:elixir][:link] do
  to "#{node[:elixir][:source][:path]}/bin"
end

# setting PATH environment variable
template "/etc/profile.d/elixir.sh" do
  mode 0755
  source "elixir.sh.erb"
  variables(:elixir_home => node[:elixir][:home])
end

```

※ erlangのotp_src_XXX.tar.gzだが、checksumが[公式サイト](http://www.erlang.org/download/MD5)の<br>
checksumを指定した所`Chef::Exceptions::ChecksumMismatch`が発生<br>
ファイル自体破損してなさそう・・・?仕方ないのでトレースからchecksumをコピーしました

- Chef-soloを実行

```
$ knife solo cook elixir-phoenix
```
<br>
正常に終了したら`ssh elixir-phoenix`でサーバーへログイン<br>
Interactive mode(rubyでいうirb)を試してみる

```
[vagrant@localhost ~]$ iex
Erlang/OTP 18 [erts-7.0] [source] [64-bit] [async-threads:10] [hipe] [kernel-poll:false]

Interactive Elixir (1.0.5) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> 40+2
42
iex(2)> "hello" <> " world"
"hello world"
iex(3)>
BREAK: (a)bort (c)ontinue (p)roc info (i)nfo (l)oaded
       (v)ersion (k)ill (D)b-tables (d)istribution

a
```
ちゃんと動作できてればOK<br>
次はPhoenixのインストールを実施します
