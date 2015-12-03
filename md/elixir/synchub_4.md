## ElixirでGitHubリポジトリ同期ツールの作成 4

今回でリポジトリと同期する処理を実装し、とりあえず完成させる<br />


#### モジュールの分離
***

前回までリポジトリ一覧の処理を`Synchub`モジュール内に書いていましたが、
一覧表示の所を別モジュールに切り出したいと思います。

新規に`lib/synchub/listrepos.ex`を以下内容で作成

```ruby
defmodule Synchub.Listrepos do

@moduledoc """
The module of list github repos.
"""

@doc "put repos name."
def put_repo_name([info|tail]) do
  IO.puts info["name"]
  put_repo_name(tail)
end

def put_repo_name(info) do
end

end
```

`lib/mix/tasks/list.github.repos.ex`を以下に編集<br />
※少し名前を変更してます。(長かったので・・・)

```ruby
defmodule Mix.Tasks.List.Github do
  use Mix.Task

  @shortdoc "Show github repos list"

  def run(args) do
    url = "users/" <> Application.get_env(:synchub, :userid) <> "/repos"
    Synchub.start
    repos = Synchub.get(url)
    Synchub.Listrepos.put_repo_name(repos)
  end
end
```

`Synchub.get`内で一覧表示させていた部分を`Synchub.Listrepos.put_repo_name(repos)`で実行しています。<br />
`$ mix list.github`で一覧表示されればOK

#### リポジトリ同期モジュールの作成
***

上でモジュールを分離させたのは、リポジトリ同期モジュールの為になります。<br />
`Synchub.get(url)`で取得したリポジトリ一覧に対して処理を行うモジュールを作成します。

`lib/synchub/syncrepos.ex`を以下内容で作成

```ruby
defmodule Synchub.Syncrepos do

@moduledoc """
The module of sync github repos.
"""

@rootdir "syncrepos"

@doc "check install git client"
def install_git?() do
  ret = System.find_executable("git") # [1]
  ret != nil
end

@doc "sync github repos."
def sync([info|tail]) do
  # create sync repos dir
  unless File.exists?(@rootdir) do
    IO.puts "create dir ==> " <> @rootdir
    File.mkdir(@rootdir)
  end
  File.cd(@rootdir)

  if File.exists?(info["name"]) do # [2]
    # git command
    File.cd(info["name"])
    IO.puts "exists repo dir " <> info["name"]
    case System.cmd("git", Application.get_env(:synchub, :exists_cmd)) do # [3]
      {err, code} -> IO.puts(err)
    end
    File.cd("../")
  else
    # clone repo
    clone_url = info["clone_url"]
    if clone_url do
      IO.puts "clone " <> clone_url
      case System.cmd("git", ["clone", clone_url]) do # [4]
        {err, code} -> IO.puts(err)
      end
    end
  end
  File.cd("../")
  sync(tail)
end

def sync([]), do: nil

end
```

やってる事のポイントとしては、

* [1] gitインストールの確認
* [2] 既にクローン済みか判定
* [3] `config.exs`で設定されたgitコマンドを実行
* [4] gitクローンを実行

になります。

#### リポジトリ同期用のタスクを作成
***

一覧表示の時と同じようにカスタムのタスクを作成したいと思います。<br />
最終的に
```
$ mix sync.github
```
としたら同期してくれるようにしたいと思います。

`lib/mix/tasks/sync.github.ex`を以下内容で作成

```ruby
defmodule Mix.Tasks.Sync.Github do
  use Mix.Task

  @shortdoc "Sync github repos"

  alias Synchub.Syncrepos

  def run(args) do
    IO.puts "start sync github repos..."
    unless Syncrepos.install_git? do # [1]
      IO.puts "not found git. please install"
    else
      url = "users/" <> Application.get_env(:synchub, :userid) <> "/repos"
      Synchub.start
      repos = Synchub.get(url)
      Syncrepos.sync(repos.body)
      IO.puts "success sync github repos"
    end
  end
end
```

[1] でgitがインストールされているか確認し、インストールされている場合のみ<br />
同期を行うようにしています。

ここまで作成したら`mix compile`でコンパイルし、`mix sync.github`で同期できれば成功です。

#### まとめ
***

荒削りですが、とりあえず作成完了ということにします(笑)<br />
プロジェクト自体は[ここ](https://github.com/Slowhand0309/Synchub)に上げています。気ままに更新していこうかなと思ってるので良かったら使ってみてください。
