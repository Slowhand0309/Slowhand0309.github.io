## ActiveSupport::Cacheを使う1

> ActiveSupport::Cacheとは

railsを構成するライブラリの中でAutoloadなどなど、便利機能を提供している<br>
ActiveSupportの中にあるキャッシュ便利ライブラリ


#### キャッシュの種類
***

1. ファイルキャッシュ
   指定したパス配下にキー単位でキャシュファイルが作成される

2. メモリキャッシュ
   メモリ上にキャシュされる。アプリ起動時のみ有効

3. Memchched
   [Memcached](http://memcached.org)のキャッシュを扱う


#### 基本的な使い方
***

↓単純にキャッシュして、読むサンプルになります。

```ruby
# encoding: utf-8
require 'active_support'

cache = ActiveSupport::Cache::MemoryStore.new
cache.fetch("foo") do
  "bar"
end
puts cache.read("foo") # => barが返却される
cache.write("hoge", "fuga")
puts cache.fetch("hoge") # => fugaが返却される
```
上の例ではメモリキャッシュを使用していますが、
基本的には他のキャッシュも同じです。

* fetch

  指定されたキーの値が存在しない場合 `do 〜`を実行し
  `bar`をキー`foo`にキャッシュします<br>
  戻りとしてキャッシュされた値が存在すれば値を返します

* read

  指定されたキーの値を取り出します<br>
  ※値が存在しない場合`nil`が返却されます

* write

  指定されたキーに第二引数の値をキャッシュします

#### キャッシュの削除
***

削除は`clear`と`cleanup`があります。

* clear

キャッシュの全てを削除します。

* cleanup

キャッシュ作成時に有効期限を設定した場合<br>
有効期限が切れたキャッシュ値を削除します

サンプル

```ruby
cache = ActiveSupport::Cache::MemoryStore.new
cache.write("hoge", "fuga")
cache.clear
puts cache.read("hoge") # => nil

cache.write("yama", "kawa")
cache.cleanup
puts cache.read("yama") # => 有効期限を設定してないためkawaが返る
```

有効期限を設定した場合

```ruby
cache.write("aaa", "bbb", {:expires_in => 1})
puts cache.read("aaa")
sleep(2)
cache.cleanup
puts cache.read("aaa") # => nilを返す
```
※ `expires_in`にキャッシュ作成日時からの経過unixtimeを指定
