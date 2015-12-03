## VagrantでAWSのEC2を使う

#### IAMユーザーの作成
***

AWSサービスにAPIでアクセスする為にアクセスキーを作成する必要があります<br />
アクセスキーを作成する為IAMユーザーを作成します

* グループの作成

まずは[IAMコンソール](https://console.aws.amazon.com/iam/home#home)にアクセスしログインする

No1

メニューから**グループ**を選択し**「新しいグループの作成」** をクリック

No2

適当なグループ名を設定します。ここでは`Developers`を設定

No3

ポリシーのアタッチではグループに権限を設定することができます。<br />
とりあえず`PowerUserAccess`に設定

No4

最後に確認画面で内容が正しければ「グループの作成」をクリックし作成する<br />
グループ一覧画面に作成したグループが表示されていればOK

No5

* ユーザーの作成

メニューから**ユーザー**を選択し**「新しいユーザーの作成」** をクリック

No6

今回は1ユーザーしか作成しないので、ユーザー名に適当な名前を入力し「作成」<br />
今回は`vagrant`の名前でユーザーを作成

No7

作成されたら「ユーザーのセキュリティ認証情報を表示」をクリックしアクセスキーをメモ<br />
または「認証情報のダウンロード」をクリックしCSVファイルで保管

No8

ユーザーにグループを紐付かせる為、ユーザー一覧から作成したユーザーをクリックし<br />
**「グループにユーザーを追加」** をクリック

No9

先ほど作成したグループを選択し「グループに追加」

No10

#### EC2側の設定
***

EC2 Management Consoleを開く

* キーペアの作成
キーペアの作成はここを参照

* セキュリティグループの作成

`ssh`と`ICMP`を通すようなセキュリティグループを作成する<br />
今回は`vagrant`の名前でセキュリティグループを作成

No11

* 起動するAMIのIDを確認

vagrantで起動したいAMIのIDを確認する為に、メニューから「インスタンスの作成」を選択

No12

今回はAmazon Linuxを起動する為、Amazon LinuxのIDを確認

No13

※リージョン毎にIDが異なるらしいので注意!!

#### Vagrant側の設定
***

* AWSプラグインのインストール

```sh
$ vagrant plugin install vagrant-aws
Installing the 'vagrant-aws' plugin. This can take a few minutes...
Installed the plugin 'vagrant-aws (0.6.0)'!
```

* Vagrantfile編集

`vagrant init`でVagrantfileができたら↓に編集

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

AWS_ACCESS_KEY_ID = "XXXXXXXXX" # 保存したアクセスキー
AWS_SECRET_KEY = "XXXXXXXXXXXX" # 保存したシークレットキー

Vagrant.configure(2) do |config|
  config.vm.box = "dummy"
  config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  config.vm.provider "aws" do |aws, override|
    aws.ami = "XXXXXXXXXXXXX" # 確認したAMI-ID
    aws.tags = { 'Name' => 'VagrantEC2' } # 適当な名前に設定
    aws.instance_type = "t2.micro" # 必要に応じて調整
    aws.security_groups = "vagrant" # 作成したセキュリティグループ
    aws.access_key_id = AWS_ACCESS_KEY_ID
    aws.secret_access_key = AWS_SECRET_KEY
    aws.region = "ap-northeast-1" # 必要に応じて調整
    aws.keypair_name = "XXXX" # 作成したキーペア
    override.ssh.username = "ec2-user"
    override.ssh.private_key_path = "~/.ssh/XXXX.pem" # 作成したキーペア
  end
end
```

`config.vm.box`は今回関係ないのでダミーを設定<br />
[公式サイト](https://github.com/mitchellh/vagrant-aws)と同様に設定

いざ!!Vagrantを起動してみる
```sh
vagrant up --provider=aws
```

エラー無く起動すればEC2のインスタンスが起動する<br />
マネジメントコンソールからも確認出来る

No14

後は普通にVagrantと同じように操作が可能

* 起動

```sh
$ vagrant ssh

       __|  __|_  )
       _|  (     /   Amazon Linux AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-ami/2015.09-release-notes/
No packages needed for security; 7 packages available
Run "sudo yum update" to apply all updates.
[ec2-user@ip-XXX-XX-XX-XXX ~]$
```

* 状態確認

```sh
$ vagrant status
Current machine states:

default                   running (aws)

The EC2 instance is running. To stop this machine, you can run
`vagrant halt`. To destroy the machine, you can run `vagrant destroy`.
```

* 停止

```sh
$ vagrant halt
```

* 削除

```sh
$ vagrant destory -f
```
