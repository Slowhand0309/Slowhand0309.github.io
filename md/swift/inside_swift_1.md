## Swiftの中を見てみる 1

最近Swiftを触っているんですが、中をちゃんと見てなかったんで、<br>
この機会に見てみようかと思います。今回はコンパイルに関してです。

###### コンパイルの流れ
***

Xcodeではビルドして実行・・・で簡単に実行できてますが、<br>
その時どのようにコンパイルされ実行されているのかを見てみます。

ざっくりとしたビルド時の流れとしては上から順に、

* **Frontend**<br>
  C/C++、Haskell、Swiftなどで書かれたソースを構文解析し<br>
  LLVM IR という中間コードを生成する

* **LLVM Optimizer**<br>
  様々な最適化を行う

* **Backend**<br>
  特定のマシンに基づいた機械語を生成

という流れになっています。

> LLVMとは?

任意のプログラミング言語に対応可能なコンパイラ基盤<br>
JavaとJava VMのように仮想マシンをターゲットにした中間コードを出力し、<br>
特定のマシンの機械語に変換する。

###### Frontend
***

C/C++, Objective‑CのFrontendに`Clang`というものがあります。<br>

これはAppleがMacOSやiOS用に作成したものです。<br>
コンパイルの流れとしては、、

* ソースコード -> `抽象構文木（Abstract Syntax Tree、AST）`
というものに変換
* LLVM IRを生成
* LLVM IR -> 機械語を生成

という流れになってます。

> Swift用のFrontend

SwiftではClangをより良くして以下のような流れになってます。

* Swiftのソースコード -> ASTに変換
* SIL(Swift intermediate Language)(LLVM IRのSwift版)を生成
* SILからLLVM IRを生成 -> 最後に機械語が生成

Clangに比べて`SIL`のフェーズが追加されています。

###### 実験
***

Swiftではコンパイルのオプションで上で見てきた、ASTやSILを<br>
出力してくれるオプションが存在します。

そのオプションを使って色々表示させてみたいと思います。

> 簡単なサンプルのコンパイル&実行

```swift
// hello.swift
print(“hello!!”)
```
↑を`hello.swift`として保存し、
```sh
$ swiftc hello.swift # コンパイル
$ ./hello # -> hello!!
```
コンパイル&実行します。

> AST表示

今度はサンプルの`AST`を表示させてみたいと思います。

```sh
$ swift -dump-ast hello.swift
(source_file
  (top_level_code_decl
    (brace_stmt
      (call_expr type='()' location=hello.swift:1:1 range=[hello.swift:1:1 - line:1:21] nothrow
      ・・・
```
なにやらずらずらっと表示されたかと思います。

> SIL表示

次は`SIL`を表示してみます。

```sh
$ swiftc -emit-silgen hello.swift | xcrun swift-demangle
sil_stage raw

import Builtin
import Swift
import SwiftShims
・・・
```

> LLVM IR表示

そして`LLVM IR`

```sh
$ swiftc -emit-ir hello.swift
; ModuleID = '-'
target datalayout = ・・・
```

> アセンブリ表示

最後に`アセンブリ表示`

```sh
$ swiftc -emit-assembly hello.swift
.section    __TEXT,__text,regular,pure_instructions
.macosx_version_min 10, 9
.globl    _main
.align    4, 0x90
_main:
.cfi_startproc
・・・
```

こうしてみると複数のフェーズを経由している事が分かります。<br>
またSwiftからObjective-Cが使えるのも同じLLVM基盤を採用しているからなんですね。
