## ElixirでGitHubリポジトリ同期ツールの作成 3

今回はテストの作成 & TravisCIで自動テストを行ってみます<br />


#### テストの作成
***

`test/synchub_test.exs`を修正していきます<br />
プロジェクト作成したばかりは以下のようになっています。

```ruby
defmodule SynchubTest do
  use ExUnit.Case

  test "the truth" do
    assert 1 + 1 == 2
  end
end
```

試しに実行してみます

```sh
$ mix test
==> poison
Compiled lib/poison.ex
Compiled lib/poison/decoder.ex
Compiled lib/poison/parser.ex
Compiled lib/poison/encoder.ex
Generated poison app
==> httpotion
Compiled lib/httpotion.ex
Generated httpotion app
==> ibrowse (compile)
==> synchub
Compiled lib/synchub.ex
Generated synchub app
.

Finished in 0.06 seconds (0.06s on load, 0.00s on tests)
1 tests, 0 failures

Randomized with seed 765702
```
依存関係が解決されテストが実行され成功しているのが分かります。<br />
次は実際に`synchub.ex`のテストを書きたいと思います。<br />
とりあえず今回はテストとして

1. リクエストのヘッダのUser-Agentに`config.exs`の`username`が設定されていること

2. レスポンスが正常に帰ってくること

を確認するテストを書いてみます。

```ruby
defmodule SynchubTest do
  use ExUnit.Case

  # 1. リクエストヘッダの確認
  test "user-agent" do
    headers = Synchub.process_request_headers(%{})
    agent = Map.fetch!(headers, :"User-Agent")
    assert agent == Application.get_env(:synchub, :username)
  end

  # 2. レスポンスの確認
  test "list.github.repos" do
    url = "users/" <> Application.get_env(:synchub, :userid) <> "/repos"
    Synchub.start
    assert_response Synchub.get(url)
  end

  defp assert_response(response) do
    assert HTTPotion.Response.success?(response, :extra)
  end

end
```

"user-agent"テストでは`process_request_headers`内で設定された
User-AgentがApplication.get_env(:synchub, :username)と等しいこと<br />
を確認しています。

"list.github.repos"テストでは実際にGithubAPIを使ってリクエストを送り<br />
レスポンスが正常に帰ってくるか確認しています。
```ruby
defp assert_response(response) do
  assert HTTPotion.Response.success?(response, :extra)
end
```
ではHTTPotionのレスポンスチェック関数を使ってます

実際にテストを実行してみます。

```sh
$ mix test
.https://api.github.com/users/Slowhand0309/repos
.

Finished in 2.4 seconds (0.1s on load, 2.2s on tests)
2 tests, 0 failures

Randomized with seed 978629
```

`0 failures`になればテスト成功です。

#### TravisCIで自動テスト
***

[TravisCI](https://travis-ci.org)でプロジェクトを登録し、プロジェクト直下に<br />
`.travis.yml`を作成します。

 * .travis.yml

```
language: elixir
elixir:
  - 1.0.5
otp_release:
  - 18.0
sudo: false
```
↑環境に合わせて修正

これでGithubにpushして自動でテストが行われれば成功です
