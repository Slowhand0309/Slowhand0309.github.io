## MechanizeでWeb操作 2 - GithubのトレンドをKindleで読む -

KindleのPaperwhiteを購入した[記事](http://developabout0309.blogspot.jp/2016/03/kindle-paperwhite-3.html)をちょっと前に書きましたが、<br>
Kindleでは電子書籍以外にも気になったWebページをKindleで読めたりします。<br>

Amazonでログインして、アカウントサービス > コンテンツと端末の管理 > <br>
設定と進んでいくと、パーソナル・ドキュメント設定という項目に<br>
自分がkindleを使っている各端末にメールアドレスが割り振られています。

img1

このメールアドレスに以下のファイル形式でドキュメントを添付して送れば<br>
Kindle端末で読めるようになります。

* Kindle形式（.MOBI、.AZW）
* Microsoft Word（.DOC、.DOCX）
* HTML（.HTML、.HTM）
* RTF（.RTF）
* Text（.TXT）
* JPEG（.JPEG、.JPG）
* GIF（.GIF）
* PNG（.PNG）
* BMP（.BMP）
* PDF（PDF）

これを利用して、Githubのトレンド(1週間分)をKindleで読むようにしたいと思います。

#### MechanizeでGithubのトレンドを収集
***

インストールや環境は[前回](http://developabout0309.blogspot.jp/2016/04/mechanizeweb-1.html)の記事を参照<br>
まずはGithubのトレンドのページの情報を取得してみます。

```ruby
require 'mechanize'

GITHUB_TOP = 'https://github.com'.freeze

agent = Mechanize.new
agent.verify_mode = OpenSSL::SSL::VERIFY_NONE
page = agent.get('https://github.com/trending?since=weekly') # (1)
page.search('h3 a').each do |link| # (2)
  url = "#{GITHUB_TOP}#{link[:href]}" # (3)
  next_page = agent.get(url)
  puts next_page.title # (4)
end
```

やってる事は、Githubのトレンド(1週間分)のページを取得(1)し、<br>
`<h3>`タグ内の`<a>`リンク情報を取得(2)、リンクのURLを取得し、<br>
完全なURLを作成しています。(3)<br>
次に、リンク先のページを取得しタイトルを表示しています。(4)

↑を実行するとリポジトリのタイトル一覧が表示されるかと思います。<br>
ただKindleに送る情報としては、リポジトリと作者名、READMEの情報を<br>
送ろうと思っているので少し修正します。

```ruby
require 'mechanize'

GITHUB_TOP = 'https://github.com'.freeze

agent = Mechanize.new
agent.verify_mode = OpenSSL::SSL::VERIFY_NONE
page = agent.get('https://github.com/trending?since=weekly')
page.search('h3 a').each do |link|
  # Delete space and line code.
  array = link.text.gsub(/(\s)/,"").split('/')
  # Get repository name.
  puts "repository : #{array[1]}" # (5)
  # Get author name.
  puts "author : #{array[0]}" # (6)
  url = "#{GITHUB_TOP}#{link[:href]}"
  next_page = agent.get(url)
  readme = next_page.at('#readme')
  puts readme.text # (7)
end
```

一応これでリポジトリ名(5)、作者名(6)、README(7)を取得することが<br>
できます。

実行する際は
```sh
$ bundle exec ruby main.rb
```
で実行できます。実行するとずらずらとコンソールに出力されるかと思います。

次回はそれをKindle端末にメール送信してみたいと思います。
