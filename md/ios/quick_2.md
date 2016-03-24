## SwiftでRSpecみたいに書けるQuickを使ってみる 2

[前回](http://developabout0309.blogspot.jp/2016/03/swiftrspecquick-1.html)の続きになりますが、<br>
色々な使い方を見てみたいと思います。

`Nimble`の`assertion`にどんなものがあるか確認してみます。<br>

#### 等価
****

`同じ値を持っているか?`をテストする

```swift
// 等しければテスト成功
expect(実際の値).to(equal(期待値))
expect(実際の値) == 期待値

// 等しくなければテスト成功
expect(実際の値).toNot(equal(期待値))
expect(実際の値) != 期待値

// 浮動小数点の等価テスト
expect(実際の値).to(beCloseTo(期待値, within: マージン値))
```

比較するオブジェクトは`Equatable`、`Comparable`プロトコルを実装または、<br>
`NSObject`のサブクラスでないと正しく比較されない

また、浮動小数点の等価テストの場合マージン値を設定します。<br>
例)
```swift
expect(10.01).to(beCloseTo(10, within: 0.1)) // -> テスト成功
```

#### 同一性
****

`同じアドレスを指しているオブジェクトか?`をテストする

```swift
// 同じアドレスを指していればテスト成功
expect(実際の値).to(beIdenticalTo(期待値))
expect(実際の値) === 期待値

// 同じアドレスを指していなければテスト成功
expect(実際の値).toNot(beIdenticalTo(期待値))
expect(実際の値) !== 期待値
```

#### 比較
****

```swift
expect(実際の値).to(beLessThan(期待値))
expect(実際の値) < 期待値

expect(実際の値).to(beLessThanOrEqualTo(期待値))
expect(実際の値) <= 期待値

expect(実際の値).to(beGreaterThan(期待値))
expect(実際の値) > 期待値

expect(実際の値).to(beGreaterThanOrEqualTo(期待値))
expect(実際の値) >= 期待値
```

こちらも`Comparable`プロトコルを実装しているオブジェクトである必要があります

#### クラス型
****

インスタンスのクラスが期待値のクラス又はサブクラスかテストします

```swift
// インスタンスがクラスのインスタスである場合テスト成功
expect(インスタンス).to(beAnInstanceOf(クラス))

// インスタンスがクラス又は寒クラスのインスタスである場合テスト成功
expect(インスタンス).to(beAKindOf(クラス))
```

#### Error Handling
****

Swift2.0の`Error Handling`のテストです。

> Error Handlingとは?

Swift2.0から導入されたJava等で言う例外処理のこと

例)
```swift
// ErrorTypeプロトコルを実装したenumを作成
enum MyError: ErrorType {
    case InvalidNumber
    case OutOfRange
}
// エラーを投げる可能性があるメソッド
func something(number: Int?) throws {
    if (number == nil) { // 不正な値
        throw MyError.InvalidNumber
    }
    if (number > 50) { // 範囲外
        throw MyError.OutOfRange
    }
}
// エラーハンドリング
do {
    try something(60)
} catch {
    print("catch error") // エラーをキャッチ
}
```

上のような`MyError`をthrowするメソッドのテストを`Quick`で行う場合<br>
`throwError`を使用してテストを行う。

```swift
// エラーがthrowされればテスト成功
// (引数に70を指定しているのでMyError.OutOfRangeがthrowされる)
expect{ try something(70) }.to(throwError())
// エラーの型が`MyError`であればテスト成功
// 引数がnilの為`MyError.InvalidNumber`をthrowする
expect{ try something(nil) }.to(throwError(errorType: MyError.self))
```
