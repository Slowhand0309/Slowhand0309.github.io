## MechanizeでWeb操作 1 - 動作確認 -

> Mechanizeとは?

Webページの操作を自動化できたりするrubyのgem<br>
[公式ページ](http://mechanize.rubyforge.org)

以前[Selenium](http://developabout0309.blogspot.jp/2015/12/rubyselenium.html)を紹介しましたが、Seleniumと異なる点は<br>
Seleniumはブラウザを起動して実際に操作しましたが、<br>
Mechanizeは内部でエミュレートします。

実際に動作させてみたいと思います。

#### インストール
***

gemをインストールします

* Gemfile

```ruby
source "https://rubygems.org"
gem "mechanize"
```

```sh
$ bundle install --path vendor/bundle
```

#### 軽く動かしてみる
***

早速試しに、このホームページのトップページの情報を取得して<br>
コンソールに出力させてみます

```ruby
require 'mechanize'

agent = Mechanize.new
page = agent.get('http://developabout0309.blogspot.jp')
puts page.body
```
↑を`main.rb`として保存し
```sh
$ bundle exec ruby main.rb
```
で実行します。<br>
成功すればずらずらっとhtmlが出力されます。<br>

Mechanizeを使用する場合まずエージェント(Mechanizeのインスタンス)を<br>
作成する必要があります。`Mechanize.new`<br>
作成したエージェントの`get`メソッドを使ってURLのページの情報を取得します<br>

#### 画像一覧を取得し保存
***

次は少し実用的に・・・ページの画像ファイルの保存方法です<br>
と言っても`Mechanize`だと結構簡単に出来ます。

`main.rb`を以下のように修正
```ruby
require 'mechanize'

agent = Mechanize.new
page = agent.get('http://sample')
page.images_with(:src => /jpeg|png|gif/).each do |img|
  img.fetch.save
end
```

`http://sample`に保存したいページのURLを設定して実行すれば、<br>
カレントディレクトリに画像ファイルが出力されているかと思います<br>

#### フォーム入力
***

フォームの入力、サブミットまでを、<br>
yahooで`mechanize`のワードで検索して、結果を出力してみたいと思います。

```ruby
require 'mechanize'

agent = Mechanize.new
page = agent.get('http://www.yahoo.co.jp')
form = page.form_with(:name => 'sf1') do |search|
 search.p = 'mechanize'
end
result = form.submit
result.links.each do |link|
   puts link.text
end
```

* 出力結果

```sh
$ bundle exec ruby main.rb
Yahoo! JAPAN
検索設定
この検索結果ページについて
楽々スクレイピング! Ruby Mechanizeの使い方 -- ぺけみさお
GitHub - sparklemotion/mechanize: Mechanize is a ruby library that ...
Rails スクレイピング手法 Mechanizeの使い方 - Qiita
RubyのMechanizeとNokogiriで読書メーターをスクレイピング - Qiita
Mechanize について - 君の瞳はまるでルビー - Ruby 関連まとめサイト
RubyでWebを操作できるMechanizeの利用例を集めてみた | Scimpr Blog
Mechanizeによるスクレイピングの基本的なことまとめ - そのねこが学ぶとき
1時間の作業を自動化して1分でやろう！ MechanizeとNokogiriで ...
mechanize | RubyGems.org | your community gem host
Class: Mechanize — Documentation for mechanize (2.7.4)
mechanize とは
mechanize IE
mechanize Ruby
fear FACTORY mechanize
www::mechanize
python+mechanize
2
3
4
5
6
7
8
9
10
次へ »
検索設定
この検索結果ページについて
```

実際にyahooで検索した結果がこちら

img1

余計なリンクまで入ってますが^^;、検索結果の一覧が取得できているかと思います。
