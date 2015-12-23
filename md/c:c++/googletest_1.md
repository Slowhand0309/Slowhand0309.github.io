## Google Testを使う

> Google Testとは?

C++用のテストフレームワーク<br>
あんましC++用のテストフレームワークはないけど、その中でも一番メジャーと思う<br>
[Google Test](https://github.com/google/googletest)

#### セットアップ
***

手順としては、`git clone`してビルド&必要なヘッダやライブラリをコピーします<br>

* まずはリポジトリからクローンしてきます。

```
$ git clone https://github.com/google/googletest.git
```

* そしてビルドしていきます

```
$ make build
$ cd build
$ cmake ..
$ make
```

`build`配下に色々できてると思いますが、<br>
この中で必要な`libgtest.a`と`libgtest_main.a`をコピーします

```
$ sudo cp ./googlemock/gtest/lib*.a /usr/local/lib
```

※ ↑Macで動作させる場合

また、ヘッダファイルもコピーしときます。

```
 $ sudo cp -r ../googletest/include/gtest /usr/local/include/
```

#### テストのテスト
***

環境はできたので、早速動かしてみたいと思います。<br>
まずはテスト対象のソースを書いていきます。

```C++:gtest.cpp
#include <gtest/gtest.h>

int add(int x, int y) {
  return x + y;
}

TEST(AddTest, Test1) {
  ASSERT_EQ(2, add(1, 1));
}
```

今回は`cmake`を使用していきます。
早速`CMakeLists.txt`を作成。

```:CMakeLists.txt
FIND_PACKAGE (GTest REQUIRED)

include_directories(
    ${GTEST_INCLUDE_DIRS}
)

add_executable(gtest gtest.cpp)
target_link_libraries(gtest_example
    pthread
     ${GTEST_BOTH_LIBRARIES}
)
```

コンパイルして実行してみる
```
$ mkdir build
$ cd build
$ cmake ..
$ make # => gtestができればOK
$ ./gtest
Running main() from gtest_main.cc
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from AddTest
[ RUN      ] AddTest.Test1
[       OK ] AddTest.Test1 (0 ms)
[----------] 1 test from AddTest (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (1 ms total)
[  PASSED  ] 1 test.
```

上のようにOKが出ればテスト成功です。
