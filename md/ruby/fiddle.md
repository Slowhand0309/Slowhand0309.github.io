## Rubyでdllやsoを扱うFiddleのまとめ

最近仕事でお世話になっている[Fiddle](http://docs.ruby-lang.org/ja/2.2.0/library/fiddle.html)のまとめです<br>

> Fiddleとは?

Fiddleが何かというと、Rubyの標準ライブラリに含まれている、<br>
Rubyからdllやsoファイルを読み込んで使えるものです。<br>
[dl](http://docs.ruby-lang.org/ja/2.1.0/library/dl.html)と同等の機能を持つ。dl自体はRuby2.0 以降deprecated となり、<br>
2.2.0 で削除されてます。

#### 簡単なサンプル
***

まずは簡単なサンプルを作ってみたいと思います。<br>

適当なディレクトリに以下の構成で作成します。
```
.
├── CMakeLists.txt
├── build ※ディレクトリ
├── sample.c
└── sample.rb
```
それぞれのファイルを編集・・

* CMakeLists.txt

```makefile
# CMakeのバージョン
cmake_minimum_required(VERSION 2.8)

# プロジェクト名
project(sample C)

# 共有ライブラリを作成
add_library(sample SHARED sample.c)

# MacOSの場合.dylibで作成される為、.soで作成されるように以下を追記
if (APPLE)
    set_property(TARGET sample PROPERTY PREFIX "lib")
    set_property(TARGET sample PROPERTY OUTPUT_NAME "sample.so")
    set_property(TARGET sample PROPERTY SUFFIX "")
endif()
```

* sample.c

```c
#include <stdio.h>

int add(int a, int b) {
  return a + b;
}

void print(const char *msg) {
  puts(msg);
}
```

* sample.rb

```ruby
# coding: utf-8
require "fiddle/import"

module M
  extend Fiddle::Importer
  dlload "build/libsample.so" # 共有ライブラリをロード
  extern "int add(int, int)" # 関数をモジュールに教えてあげる
  extern "void print(const char *)"
end

# いざ実行!!
puts M.add(1, 2) #=> 3

M.print('hoge') #=> hoge
```

共有ライブラリ(so)側は単純に引数を加算して返すだけの`add`関数と<br>
引数の文字列を表示する`print`関数を実装。<br>
これを`sample.rb`で呼び出して実行しています。

ビルドして実行します。
```sh
$ cd build && cmake .. && make && cd ..
$ ruby sample.rb
```

こんな感じで単純な関数を呼ぶくらいであれば、<br>
お気軽に使うことができます。

#### 複雑なサンプル
***

実際にゴリゴリ使うとなったら上のサンプルのようにはいきません<br>
例えばこんな構造体があったとします・・・
```c
struct Person {
  int no;
  wchar_t name[20];
};

struct Members {
  struct Person persons[3];
  struct Members *nextPtr;
};
```
これをrubyから扱うとかちょっと考えたくないですが、<br>
実際にやるとこうなります。

* sample.c

```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <wchar.h>

struct Person {
  int no;
  wchar_t name[20];
};

struct Members {
  struct Person persons[3];
  struct Members *nextPtr;
};

void initMember(struct Members **ppMembers) {
  *ppMembers = (struct Members *)malloc(sizeof(struct Members));
  (*ppMembers)->persons[0].no = 1;
  memset((*ppMembers)->persons[0].name, 0, sizeof(wchar_t) * 20);
  wcscpy((*ppMembers)->persons[0].name, L"hogehoge1");
  (*ppMembers)->persons[1].no = 2;
  memset((*ppMembers)->persons[1].name, 0, sizeof(wchar_t) * 20);
  wcscpy((*ppMembers)->persons[1].name, L"hogehoge2");
  (*ppMembers)->persons[2].no = 3;
  memset((*ppMembers)->persons[2].name, 0, sizeof(wchar_t) * 20);
  wcscpy((*ppMembers)->persons[2].name, L"hogehoge3");

  // Next pointer
  struct Members *next = (struct Members *)malloc(sizeof(struct Members));
  next->persons[0].no = 11;
  memset(next->persons[0].name, 0, sizeof(wchar_t) * 20);
  wcscpy(next->persons[0].name, L"fugafuga1");
  next->persons[1].no = 12;
  memset(next->persons[1].name, 0, sizeof(wchar_t) * 20);
  wcscpy(next->persons[1].name, L"fugafuga2");
  next->persons[2].no = 13;
  memset(next->persons[2].name, 0, sizeof(wchar_t) * 20);
  wcscpy(next->persons[2].name, L"fugafuga3");
  (*ppMembers)->nextPtr = next;
}

void freeMember(struct Members **ppMembers) {
  free((*ppMembers)->nextPtr);
  free(*ppMembers);
}

```
サンプルなのでエラー等は一切考慮してません。

* sample.rb

```ruby
# coding: utf-8
require "fiddle/import"

module M
  extend Fiddle::Importer
  dlload "build/libsample.so"
  extern "void initMember(void *)"
  extern "void freeMember(void *)"

  # 構造体をruby用に定義
  # 構造体in構造体は展開して定義しないといけない
  # wchar_tはMacOSだと4byte, windowsだと2byte
  # 今回MacOSなのでintをあてがってます
  MEMBERS = struct([
    "int no1",
    "int name1[20]",
    "int no2",
    "int name2[20]",
    "int no3",
    "int name3[20]",
    "void *nextPtr"
    ]);
end

# void *分の領域を確保
pointer = Fiddle::Pointer.malloc(Fiddle::SIZEOF_VOIDP)
M.initMember(pointer)
# Members構造体へキャスト
members = M::MEMBERS.new(pointer.ptr)
puts members.no1 #=> 1
puts members.name1.pack('U*') #=> hogehoge1
puts members.no2 #=> 2
puts members.name2.pack('U*') #=> hogehoge2
puts members.no3 #=> 3
puts members.name3.pack('U*') #=> hogehoge3

# nextPtrをMembers構造体へキャスト
members2 = M::MEMBERS.new(members.nextPtr)
puts members2.no1 #=> 11
puts members2.name1.pack('U*') #=> fugafuga1
puts members2.no2 #=> 12
puts members2.name2.pack('U*') #=> fugafuga2
puts members2.no3 #=> 13
puts members2.name3.pack('U*') #=> fugafuga3

M.freeMember(pointer)
```
とまあ、何とも強引な感じになりますw
