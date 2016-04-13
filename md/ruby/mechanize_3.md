## MechanizeでWeb操作 3 - GithubのトレンドをKindleで読む -

[前回](http://developabout0309.blogspot.jp/2016/04/mechanizeweb-2-githubkindle.html)の続きで、
今回は収集した情報をKindle端末にメール送信してみたいと思います。


#### メール送信
***

メール送信するに当たり`Gemfile`に以下のgemを追加します。
```ruby
gem "mail"
```

`main.rb`で追加したgemをrequireします
```ruby
require 'mail'
```

メール送信部分の処理を追加します。gmailを使って送信を行います。<br>
```ruby
# 送信先メールアドレス(Kindle端末)
SEND_TO = 'XXXXXXX@kindle.com'.freeze
# 送信元メールアドレス(Gmail)
GMAIL_ADR = 'XXXXXXX@gmail.com'.freeze
# Gmailアカウントのパスワード
PASSWORD = 'XXXXXXXXXXXX'.freeze
# メール件名
SUBJECT = 'Github trends for a week.'.freeze

# メール送信メソッド
def send_mail

  # Gmail用のsmtpオプション
  options = {
            :address              => "smtp.gmail.com",
            :port                 => 587,
            :domain               => "smtp.gmail.com",
            :user_name            => GMAIL_ADR,
            :password             => PASSWORD,
            :authentication       => :plain,
            :enable_starttls_auto => true  }

  mail = Mail.new
  mail.to = SEND_TO
  mail.from = GMAIL_ADR
  mail.subject = SUBJECT
  mail.delivery_method(:smtp, options)
#  mail.add_file(※添付ファイル)

  mail.deliver
end
```
メールアドレスやパスワードは置き換えて下さい。<br>
※`Net::SMTPAuthenticationError`が発生した場合は認証の設定が必要です。<br>
セキュリティ上、不明なアプリからのログインは通常認められていない為、<br>
* 安全性の低いアプリからのアクセスを許可する
* 2段階認証をONにしてアプリ用のパスワードを発行する

のいずれかを行う必要があります。<br>
[アカウント設定](https://myaccount.google.com/)から設定できます。

#### メールに添付するhtmlドキュメント作成
***

Kindleに送信するメールの添付ファイルとして今回は`html`ドキュメントを<br>
添付します。※添付可能なフォーマットは[前回](http://developabout0309.blogspot.jp/2016/04/mechanizeweb-2-githubkindle.html)を参照

処理としては、Mechanizeで取得したトレンドデータをストックし、<br>
`erb`を使って`html`に展開します。

```ruby
# Hashキー : リポジトリ名
KEY_REPOSITORY = :repository.freeze
# Hashキー : 作者名
KEY_AUTHOR = :author.freeze
# Hashキー : readme
KEY_README = :readme.freeze

# テンプレートファイル名
TEMPLATE_FILE = 'trends.html.erb'.freeze
# 作成htmlドキュメントファイル名
OUTPUT_FILE = 'trends.html'.freeze

# htmlドキュメント作成メソッド
def gen_html(trends)
  erb = ERB.new(File.read(TEMPLATE_FILE), nil, '-')
  ret = erb.result(binding)
  File.open(OUTPUT_FILE, "w:utf-8") do |f|
    f.write(ret)
  end
end

agent = Mechanize.new
agent.verify_mode = OpenSSL::SSL::VERIFY_NONE
page = agent.get('https://github.com/trending?since=weekly')

trends = []
page.search('h3 a').each do |link|
  trend = {}
  array = link.text.gsub(/(\s)/,"").split('/')
  # リポジトリ名の取得
  trend[KEY_REPOSITORY] = array[1]
  # 作者名の取得
  trend[KEY_AUTHOR] = array[0]

  # リポジトリのページへ遷移
  url = "#{GITHUB_TOP}#{link[:href]}"
  next_page = agent.get(url)
  # readmeの情報取得
  readme = next_page.at('#readme')
  trend[KEY_README] = readme
  # 配列に追加
  trends << trend
end

# html作成
gen_html(trends)
```

* テンプレートファイル

```ruby
<!DOCTYPE HTML>
<html>
  <head>
    <meta charset="utf-8">
    <title>Github trends.</title>
  </head>
  <body>
<% trends.each do |trend| -%>
  <h1><%= trend[:repository] %></h1>
  <p><%= trend[:author] %></p>
  <hr>
  <pre><%= trend[:readme] %></pre>
  <br>
<% end -%>
  </body>
</html>

```

#### 完成
***

これで一通りできたので、実際に試してみます。<br>
Kindle端末に送信されれば成功です。

* 送信されたドキュメントを開いた様子

img1

画像やgifなどが表示されないのが残念な感じですが ><;<br>
一応トレンドのリポジトリとreadme一覧がKindleで読めます^^;

今回はテストでやってみましたが、今後色々と改良していきたいと思います。

* 完成したソース

```ruby
# coding: utf-8
require 'erb'
require 'mail'
require 'mechanize'

GITHUB_TOP = 'https://github.com'.freeze

# Hashキー : リポジトリ名
KEY_REPOSITORY = :repository.freeze
# Hashキー : 作者名
KEY_AUTHOR = :author.freeze
# Hashキー : readme
KEY_README = :readme.freeze

# テンプレートファイル名
TEMPLATE_FILE = 'trends.html.erb'.freeze
# 作成htmlドキュメントファイル名
OUTPUT_FILE = 'trends.html'.freeze

# 送信先メールアドレス(Kindle端末)
SEND_TO = 'XXXXXXX@kindle.com'.freeze
# 送信元メールアドレス(Gmail)
GMAIL_ADR = 'XXXXXXX@gmail.com'.freeze
# Gmailアカウントのパスワード
PASSWORD = 'XXXXXXXXXXXX'.freeze
# メール件名
SUBJECT = 'Github trends for a week.'.freeze

# メール送信メソッド
def send_mail

  # Gmail用のsmtpオプション
  options = {
            :address              => "smtp.gmail.com",
            :port                 => 587,
            :domain               => "smtp.gmail.com",
            :user_name            => GMAIL_ADR,
            :password             => PASSWORD,
            :authentication       => :plain,
            :enable_starttls_auto => true  }

  mail = Mail.new
  mail.to = SEND_TO
  mail.from = GMAIL_ADR
  mail.subject = SUBJECT
  mail.delivery_method(:smtp, options)
  mail.add_file(OUTPUT_FILE)

  mail.deliver
end

# htmlドキュメント作成メソッド
def gen_html(trends)
  erb = ERB.new(File.read(TEMPLATE_FILE), nil, '-')
  ret = erb.result(binding)
  File.open(OUTPUT_FILE, "w:utf-8") do |f|
    f.write(ret)
  end
end

agent = Mechanize.new
agent.verify_mode = OpenSSL::SSL::VERIFY_NONE
page = agent.get('https://github.com/trending?since=weekly')

trends = []
page.search('h3 a').each do |link|
  trend = {}
  array = link.text.gsub(/(\s)/,"").split('/')
  # リポジトリ名の取得
  trend[KEY_REPOSITORY] = array[1]
  # 作者名の取得
  trend[KEY_AUTHOR] = array[0]

  # リポジトリのページへ遷移
  url = "#{GITHUB_TOP}#{link[:href]}"
  next_page = agent.get(url)
  # readmeの情報取得
  readme = next_page.at('#readme')
  trend[KEY_README] = readme
  # 配列に追加
  trends << trend
end

# html作成
gen_html(trends)
# メール送信
send_mail

```
