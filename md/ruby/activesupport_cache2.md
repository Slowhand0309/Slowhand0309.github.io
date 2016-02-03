## ActiveSupport::Cacheを使う2

今回は`ActiveSupport::Cache::FileStore`と<br>`ActiveSupport::Cache::MemCacheStore`をやってみたいと思います


#### ActiveSupport::Cache::FileStore
***

まずはファイルキャッシュです<br>
ファイルとしてキャッシュするので、当たり前ですがIOアクセス分
メモリキャシュと比べて遅くなります。

ちなみにどれくらい差があるかを計測してみます

```ruby
# encoding: utf-8
require 'active_support'
require 'benchmark'

memstore = ActiveSupport::Cache::MemoryStore.new
fstore = ActiveSupport::Cache::FileStore.new('fcache')

Benchmark.bm 10 do |r|
  r.report "MemoryStore" do
    1000.times do |i|
      key = "oh" + i.to_s
      value = "no" + i.to_s
      memstore.write(key, value)
      memstore.read(key)
    end
  end
  r.report "FileStore" do
    1000.times do |i|
      key = "oh" + i.to_s
      value = "no" + i.to_s
      fstore.write(key, value)
      fstore.read(key)
    end
  end
end
```

↑では1000回キャシュに書き込んで、読み込むという処理を`MemoryStore`と<br>
`FileStore`で行っています。

結果

```
user     system      total        real
MemoryStore  0.030000   0.000000   0.030000 (  0.038875)
FileStore    0.500000   0.550000   1.050000 (  1.162233)
```

全体で約35倍、IOアクセスのシステムコール抜きにしても約16倍ほど違います。<br>
この時のキャッシュファイルとして指定パス配下に以下のような構成でファイルが作成されます

```
├── fcache
│   ├── 108
│   │   └── 500
│   │       └── oh0 # キー oh0
│   ├── 109
│   │   └── 510
│   │       └── oh1
・・・
│   ├── 17C
│   │   ├── 110
│   │   │   └── oh299
│   │   ├── 120
│   │   │   └── oh389
│   │   ├── 130
│   │   │   ├── oh398
│   │   │   └── oh479
```

1キーに対して1ファイルという構成です。(例ではoh0〜oh999ファイル作成される)


#### ActiveSupport::Cache::MemCacheStore
***

永続性のないものをキャッシュする際に便利な[memcached](http://memcached.org)を使ったものです。<br>
環境はVagrant(CentOS6.7)で作成しmemcachedをインストールしたものを使用します

```sh
$ vagrant init
$ vim Vagrantfile # => IP設定と11211のポート転送設定
  config.vm.network "private_network", ip: "192.168.XXX.XXX"
  config.vm.network "forwarded_port", guest: 11211, host: 11211
$ vagrant up
$ vagrant ssh
$ sudo yum -y install memcached
```

MemCacheStoreを使用する場合`dalli`というgemが必要になります。<br>
※ [dalli](https://github.com/petergoldstein/dalli)はrubyでmemcachedを扱うgemです

```sh
$ vim Gemfile # => gem 'dalli'を追加
$ bundle install --path vendor/bundle
```

後は同じようにキャッシュに保存したものを取り出してみます。

```ruby
memcached = ActiveSupport::Cache::MemCacheStore.new("192.168.XXX.XXX:11211")
memcached.write("hoge", "fuga")
puts memcached.read("hoge") # => fuga
```

特有の処理として`MemCacheStore.new`で引数にIPアドレスとポートを渡しています。<br>
引数なしでnewした場合`localhost:11211`で接続されます。
