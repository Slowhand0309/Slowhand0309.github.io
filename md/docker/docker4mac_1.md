## Docker for MacでプライベートなDockerHubを作る

DockerHubは沢山のimageが公開されていて便利ですが、<br>
自分用や会社用のDockerHubを作りたい時があるかと思います。<br>
そんな時はDockerを使ってDockerHubを作成できるので作ってみます。

#### 環境構築
****

最近はLinuxに限らず、WindowsでもMacでもDockerが動きます。<br>
今回は`Docker for Mac`を使ってMacに環境を構築してみます。

まずは<br>
[ここ](https://docs.docker.com/docker-for-mac/)
から`dmg`ファイルをダウンロード。

インストールが完了するとステータスバーにDockerのアイコンが表示されます<br>

img1

最初アイコンをクリックすると↓のようなポップアップが表示され<br>
Dockerの使用状況を改善の為に送るかどうか設定できます。<br>

img2

「Got it」をクリックし準備OKです。

ちゃんと動いているかターミナルから
```sh
$ docker --version
```
を実行し、バージョンが表示されればOKです。

#### Registryコンテナの取得
****

公式に配布されているRegistryコンテナを取得し、<br>
プライベートなDockerHubを構築していきます。

まずはimageをpull
```sh
$ docker pull registry:2
```

ちゃんとpullできたか`docker images`で確認します。
```sh
$ docker images
REPOSITORY  TAG   IMAGE ID         CREATED        SIZE
registry    2     XXXXXXXXXXXX     4 weeks ago    33.3 MB
```

#### 起動&可視化
****

早速起動して使ってみたいと思います・・が、<br>
その前にDockerHubみたいにブラウザで登録状況が確認できた方が<br>
使いやすいので、可視化するコンテナの<br>
[docker-registry-frontend](https://hub.docker.com/r/konradkleine/docker-registry-frontend/)<br>
を使います。

毎回コマンドで起動して〜停止して〜というのもきついので<bt>
以下のスクリプトを用意します。

* run_registry.sh

```sh
#!/usr/bin/env bash

DOCKER_HOME=$PWD

if [ -e $DOCKER_HOME/registry ]; then
  # 何もしない
else
  mkdir $DOCKER_HOME/registry
fi

docker run \
	-d \
	-p 5000:5000 \
	-v $DOCKER_HOME/registry:/var/lib/registry \
	--name docker_registry2 \
	registry:2

docker run \
	-d \
	-e ENV_DOCKER_REGISTRY_HOST=[ローカルPCのIPアドレス] \
	-e ENV_DOCKER_REGISTRY_PORT=5000 \
	-p 8080:80 \
	--name registry_browser \
	konradkleine/docker-registry-frontend:v2
```

`-v $DOCKER_HOME/registry:/var/lib/registry \`では<br>
登録されたimageの内容はコンテナ内の`/var/lib/registry`に<br>
書き込まれます。永続化の為ホストの`$DOCKER_HOME/registry`に<br>
マウントさせています。

2番目の`docker run`では可視化の為のコンテナを走らせてます。

早速起動します。
```sh
$ ./run_registry.sh
```

初回はimageのダウンロードで遅いかもしれません。起動ができたら<br>
`http://localhost:8080`にアクセスし以下の画面が出れば成功です。

img3

#### push&pull
****

作成したプライベートなDockerHubにimageをpushする場合は、<br>
まずpushしたいimageを特定の名前で作成します。

例えば、`jenkins:latest`のイメージから名前を帰る場合、<br>
[レジストリのIP]:[ポート]/[リポジトリ名]>/[イメージ名]:[tag]<br>
で作成します。

例)
```sh
$ docker tag jenkins:latest localhost:5000/slowhand/jenkins:latest
```

あとはpushすればOKです。
```sh
$ docker push localhost:5000/slowhand/jenkins:latest
```

`http://localhost:8080`上で登録されてると思います。

img4

逆にpullする場合は
```sh
$ docker pull localhost:5000/slowhand/jenkins:latest
```
でOKです。

#### 停止
****

停止用のスクリプトも書いておきます。

* stop_registry.sh

```sh
#!/usr/bin/env bash

docker stop registry_browser
docker stop docker_registry2

docker rm registry_browser
docker rm docker_registry2
```

以上、色々便利になりましたw
