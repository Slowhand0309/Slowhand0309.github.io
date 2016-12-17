## DockerのテストをServerSpec+CircleCIでやる

Dockerfileで作成したimageをテストしてみたいと思います。<br>
ServerSpecとCircleCIを使います。

サンプルとして作成するimageはAndroidがビルドできる環境のimageを作成します。

#### 最終的な構成
****

↓の構成でリポジトリを作成します。

```
.
├── circle.yml
├── run-tests.sh   # 各imageのテストを実行
├── android        # image毎にサブディレクトリを作成
│   ├── Dockerfile
│   ├── docker-build.sh # docker build用のスクリプト
│   └── spec
│       ├── Gemfile
│       └── spec
│           ├── android_docker_spec.rb
│           └── spec_helper.rb
├── xxxxxxx
│
```

リポジトリのRootに`circle.yml`があり、各image毎にサブディレクトリを<br>
作成し、テストの時はリポジトリ配下にある全imageをテストします。


#### Androidビルド環境image
****

まずはAndroidビルド環境用のDockerfileを作成

```
# Image for android dev.
#
FROM java:openjdk-8-jdk
ENV HOME_DIR /usr/local
WORKDIR $HOME_DIR

# Update packages.
RUN apt-get update && apt-get -y install sudo

# Install wget for download android sdk.
RUN sudo apt-get -y install wget

RUN sudo apt-get -y install lib32stdc++6 lib32z1

# Download android sdk.
RUN wget http://dl.google.com/android/android-sdk_r24.4.1-linux.tgz
RUN tar zxvfo android-sdk_r24.4.1-linux.tgz
RUN rm -rf android-sdk_r24.4.1-linux.tgz

# Environment variables
ENV ANDROID_HOME /usr/local/android-sdk-linux
ENV PATH $ANDROID_HOME/platform-tools:$ANDROID_HOME/tools:$PATH

RUN echo y | android update sdk --no-ui --all --filter "android-23,build-tools-25.0.0"
RUN echo y | android update sdk --no-ui --all --filter "extra-android-m2repository"
```

やってる事はAndroid SDKをダウンロードし、`ANDROID_HOME`の環境変数を設定<br>
最後にplatformとbuild-tools、supportライブラリをダウンロードしています。

ビルド用のスクリプトを用意しておきます。

```sh
#!/usr/bin/env bash
# Build docker image for Circle CI

docker build -t slowhand/android:1.0 .
```
imageの名前は好きな名前で。<br>
試しにスクリプトを実行させてみてimageができていればOKです。

#### Spec
****

ここからテストを作成していきます。<br>
今回は`serverspec`を使うので`Gemfile`を用意します。

```ruby
source "https://rubygems.org"

gem "docker-api"

group :test do
  gem 'specinfra', '2.12.7'
  gem 'serverspec'
end
```

`serverspec`でDockerを扱う`docker-api`も入れておきます。<br>

次に`spec_helper.rb`と実際のテストコードを記述する`android_docker_spec.rb`です

* spec_helper.rb

  ```ruby
  # encoding: utf-8
  require 'docker'
  require 'serverspec'

  set :backend, :docker
  set :docker_url, ENV['DOCKER_HOST']
  set :docker_image, 'slowhand/android:1.0'

  Excon.defaults[:ssl_verify_peer] = false
  ```

* android_docker_spec.rb

  ```ruby
  # encoding: utf-8
  require 'spec_helper'

  if ENV['CIRCLECI']
    class Docker::Container
      def remove(options={}); end
      alias_method :delete, :remove
    end
  end

  %w(wget lib32stdc++6 lib32z1).each do |pkg|
    describe package(pkg) do
      it { should be_installed }
    end
  end

  describe file('/usr/local/android-sdk-linux') do
    it { should be_directory }
  end

  describe command('env') do
    its(:stdout) { should match /ANDROID_HOME/ }
  end
  ```

  ↑の通りですが、 `wget lib32stdc++6 lib32z1`のインストール確認と<br>
  `$ANDROID_HOME`のパスと環境変数の確認をテストしています。

#### CircleCI
****

最後に`circle.yml`とテストを起動させるスクリプトです。

* circle.yml

  ```yml
  machine:
    services:
      - docker

  dependencies:
    override:
      - docker info

  test:
    override:
      - ./run-tests.sh
  ```

  ↑ではテスト時に`run-tests.sh`を起動するだけのシンプルなものです。

* run-tests.sh

  ```sh
  #!/usr/bin/env bash

  WORKDIR=$PWD

  # Find target directory.
  for entity in `find . -type d -mindepth 1 -maxdepth 1 -not -name ".git"`; do
    # Only exist spec directory.
    if [ -e $entity/spec ]; then
      # Build docker image.
      cd $entity
      ./docker-build.sh

      cd spec
      # Install gems via bundler.
      bundle install --path vendor/bundle
      bundle exec rspec
      cd $WORKDIR
    fi
  done
  ```

  少し込み入ってますが、image用のサブディレクトリ内に`spec`ディレクトリがあれば<br>
  中に入ってテストを実行しています。

ここまでくれば、あとはpushすればCIが動作しテストを行ってくれます。<br>
[こちら](https://github.com/Slowhand0309/dockerfiles)で公開してますので宜しかったらどうぞ。
