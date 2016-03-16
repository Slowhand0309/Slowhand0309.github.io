## VMとの紐付きがおかしくなった場合

現象としては、`Vagrant`ファイルがあるディレクトリで<br>
`vagrant up`すると違ったVMが立ち上がってしまうというもの・・・

#### 確認
***

おかしいと思ってVirtualBoxを起動すると同じものが2つある!?<br>
そこで現在のVM一覧を見るコマンド`VBoxManage list vms`を叩いて確認してみると

```
$ VBoxManage list vms
・・・
"hogehoge_default_1457750943558_68266" {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}
"hogehoge_default_1457837316012_51675" {YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY}
```

やっぱし同じものが2つある(↑**hogehoge**が2つ)<br>
※XXXXやYYYY~には実際は英数字が入ります

#### 対策
***

紐付きがおかしくなっている事は間違いないので、`Vagrant`ファイルがある<br>
場所の直下にある`.vagrant`ディレクトリを調べて見る

* 階層
```
.vagrant
└── machines
    └── default
        └── virtualbox
            ├── action_provision
            ├── action_set_name
            ├── id ★
            ├── index_uuid
            ├── private_key
            └── synced_folders
```

★が付いている`id`が怪しいので中を見てみると・・
```
$ cat .vagrant/machines/default/virtualbox/id
YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY
```

ん〜恐らく、ここが`XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX`じゃないかと・・<br>
修正して`vagrant up`してみると、<br>
**望んていたものが起動しました!!**

これで一件落着かと思っていると、起動や停止する度にパスワードを聞かれる羽目に!?

#### 原因
***

`vagrant up`や`vagrant halt`するとVMとssh接続を行っており、
認証がうまくいってないのが原因でした。

↑の`.vagrant`の階層で`private_key`というのが秘密鍵で、<br>
別のVM(YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY)の秘密鍵が残っちゃっていた模様。

#### 対策
***

解決策としては、

* 全く新規に公開鍵、秘密鍵を作成
* 残ってる秘密鍵を元に公開鍵を作成

の2つがあります。<br>
今回は残ってる秘密鍵を元に公開鍵を作成したいと思います。

秘密鍵から公開鍵を作成
```sh
$ ssh-keygen -y -f .vagrant/machines/default/virtualbox/private_key > authorized_keys
```

VMを起動し、`/home/vagrant/.ssh`の`authorized_keys`を上書きます
```sh
$ mv /vagrant/authorized_keys /home/vagrant/.ssh/.
```

これで認証が失敗せず、パスワード聞かれることもなくなります。

#### その他
***

Vagrantを起動する際は`Vagrant`ファイルがあるディレクトリに移動して<br>
`vagrant up`をしていましたが、`vagrant up {id}`とする事で<br>
どの場所からでも起動できます。

`{id}`を確認するには
```sh
$ vagrant global-status
{id}  default virtualbox poweroff hoge
{id}  default virtualbox poweroff fuga
```
で確認できます。

また、`vagrant up`に限らずその他のコマンドでも使用できます。<br>
`vagrant halt {id}`、`vagrant destroy {id}`・・・
