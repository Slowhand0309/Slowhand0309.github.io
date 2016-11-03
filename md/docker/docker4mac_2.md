## Docker Composeの利用

前回の記事で複数のコンテナを起動/停止する際に<br>
シェルを作成し実行していましたが、<br>
**Docker Compose**を使うとそこら辺をよろしくやってくれます。

`Docker for Mac`をインストールすると一緒にインストールされます。

#### docker-compose.ymlの作成
****

前回はと言うと・・・
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
このようなシェルを書いてました。<br>
これを`docker-compose.yml`ファイルを作成し以下のように記述します。

```yml
docker_registry2:
  image: registry:2
  ports:
    - "5000:5000"
  volumes:
    - "./registry:/var/lib/registry"
registry_browser:
  image: konradkleine/docker-registry-frontend:v2
  ports:
    - "8080:80"
  environment:
    ENV_DOCKER_REGISTRY_HOST: [ローカルPCのIPアドレス]
    ENV_DOCKER_REGISTRY_PORT: 5000
```

シェルとの対比は見れば分かるとは思います。

`docker-compose.yml`がある場所で
```sh
$ docker-compose up -d
```
を実行すると依存関係なんかをよろしくやってくれて、<br>
コンテナが起動します。

また、
```sh
$ docker-compose stop
```
でコンテナをまとめて終了させる事ができます。

最後に
```sh
$ docker-compose rm
```
でまとめてコンテナを削除します。

便利ですw
