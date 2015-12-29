## Ruby+Seleniumでブラウザ自動操作

ブラウザの自動操作ができるseleniumをrubyでサクッと使ってみる<br>

#### Gemのインストール
***

gemで提供されているので早速インストール

```sh
$ bundle init
$ vim Gemfile
```

`Gemfile`を以下の内容に修正

```ruby
source "https://rubygems.org"
gem "selenium-webdriver"
```

インストール
```sh
$ bundle install --path vendor/bundle
```

#### ブラウザ起動と終了
***

まずは、単純にブラウザを起動して終了するだけのシンプルなものを作成します<br>
↓の内容で`main.rb`を作成
```ruby
# coding: utf-8
require 'selenium-webdriver'

driver = Selenium::WebDriver.for :firefox # ブラウザ起動
driver.quit # ブラウザ終了
```

早速実行してみます<br>
※ブラウザFirefoxがインストール済み前提
```sh
$ bundle exec ruby main.rb
```

Firefoxが起動して終了すれば成功

#### 検索の自動化
***

次はブラウザを起動して指定したキーワードで検索し、検索結果を<br>
スクリーンショットで保存してみたいと思います。

先ほどの`main.rb`を以下内容に変更

```ruby
# coding: utf-8
require 'selenium-webdriver'

driver = Selenium::WebDriver.for :firefox # ブラウザ起動
driver.navigate.to 'http://www.yahoo.co.jp/' # URLを開く

# テキストフィールドへ入力
element = driver.find_element(:id, 'srchtxt')
element.send_keys('Selenium')

# 検索ボタンをクリック
driver.find_elements(:id, 'srchbtn')[0].click
driver.save_screenshot('./screenshot.png') # スクリーンショットを保存
driver.quit # ブラウザ終了
```

いざ実行!! ↓のように起動しキーワードを検索すれば成功

* gif

保存されたスクリーンショットがこちら↓

* png
