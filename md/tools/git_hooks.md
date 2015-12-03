## Git フック - サーバーサイド -

最近gitのフックを触ることがあったのでメモ

> git hookとは

特定のタイミング(commitした、pushしたなど)でスクリプトを起動することができる<br />
スクリプトはクライアントとサーバーサイドに分けられれ`.git/hooks`にスクリプトを配置する

#### サーバーサイドフック一覧
***

サーバーサイドつまりはリモートリポジトリ側でフックする時に対応しているスクリプト一覧

* pre-receive

  クライアントからpushされた時に最初に実行されるスクリプト<br />
  戻り値を0以外の値で終了させるとpushがなかったことにできる

* update

  `pre-receive`は一度のみ呼ばれるのに対し`update`はブランチ単位でそれぞれ実行される

* post-receive

  pushが完了したら一度だけ呼ばれる

* post-update

  最後に呼ばれこのスクリプトがコマンド結果自体に影響は及ぼさない


どんな引数が渡ってくるのか不明だったので、それぞれのスクリプトがどんな引数を受け取るか調査<br />
それぞれのスクリプトを以下に編集

```sh
#!/bin/sh
echo "スクリプト名 >>" >> up.log
echo $# >> up.log
echo $@ >> up.log
echo "--------------" >> up.log
```

↓実行結果
```
pre-receive >>
0

--------------
update >>
3
refs/heads/master 9848db4829aac05708fcdd369ced6e2e90647322 a3cea1587a51c59b48911c5fadc8e362f2bbaa19
--------------
post-receive >>
0

--------------
post-update >>
1
refs/heads/master
--------------
```

結果から引数を撮るのは`update`と`post-update`のみ<br />
updateには引数として**1:参照名, 2:push前のSHA, 3:新しいSHA**

<br />
#### コミッターとメッセージ取得
***

pushされた全コミットのコミッターとメッセージを取得するスクリプトを書いてみようと思います<br />
スクリプトは扱いやすい`ruby`を使って書いてみました

* **update**

```ruby
#!/usr/bin/env ruby

refname = ARGV[0]
oldrev  = ARGV[1]
newrev  = ARGV[2]

puts "push info >> \n(#{refname}) (#{oldrev[0,6]}) (#{newrev[0,6]})"

sharevs = `git rev-list #{oldrev[0,6]}..#{newrev[0,6]}`.split("\n")
sharevs.each do |rev|
  message = `git cat-file commit #{rev} | sed '1,/^$/d'`

  committer = "?"
  messages = `git cat-file commit #{rev}`.split("\n")
  messages.each do |msg|
    committer = msg if msg.include?("committer")
  end

  puts committer
  puts message
end
```

まず、3つの引数の情報を取得し、`git rev-list`コマンドを使用してpush分の全SHA値を取得しています

例) `git rev-list 旧SHA..新SHA`
```sh
➜  githook git:(master) git rev-list 460477..a3cea15
a3cea1587a51c59b48911c5fadc8e362f2bbaa19
9848db4829aac05708fcdd369ced6e2e90647322
079c9649b95fad9e48288f320f40bf6a4e4a552b
※ 旧SHAは表示されない!!
```

次に`git cat-file`コマンドを使ってコミットの情報を取得しています

例) `git cat-file commit SHA値`
```sh
➜  githook git:(master) git cat-file commit a3cea1587
tree cb06c6f35f32204a91f96d60051260eb0fe3c06e
parent 9848db4829aac05708fcdd369ced6e2e90647322
author slowhand0309 <slowhand0309@gmail.com> 1445675171 +0900
committer slowhand0309 <slowhand0309@gmail.com> 1445675171 +0900

git message.
```

`git cat-file commit #{rev} | sed '1,/^$/d'`では
空白行以下のメッセージ=commitメッセージを取得しています<br />

今回は取得したコミッターやメッセージをputsしているだけ<br />
(※標準出力するとクライアントのpushした時にメッセージ表示されます)ですが<br />
色々な事が出来るので遊んでみようと思います
