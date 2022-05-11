---
title: "Node.jsで標準ライブラリだけを使って単体テストを行う方法【動画版あり】"
emoji: "🙆‍♂️"
type: "tech"
topics: ["nodejs", "test"]
published: true
---

## この記事について

この記事ではNode.jsの標準ライブラリのassertだけを使って単体テストを行う方法について紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/4e90436db3bf48ab6e840c098c701ea3#file-mymath-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/4e90436db3bf48ab6e840c098c701ea3)
- [動画版](https://www.youtube.com/watch?v=wkDnKNYax8E)

https://www.youtube.com/watch?v=wkDnKNYax8E



## おおまかな手順

おおまか手順を下記に示します。

1. ソースコードの作成準備
2. ソースコードの作成
3. テストコードの作成準備
4. テストコードの作成
5. 動作確認



## ソースコードの作成準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-test-unit
cd nodejs-test-unit
touch MyMath.js
```



## ソースコードの作成

### MyMath.js

エディタでMyMath.jsを開いて下記の内容を入力します。

[クリックしてMyMath.jsへ移動](#file-mymath-js)



## テストコードの作成準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
touch MyMathTest.js
```



## テストコードの作成

### MyMathTest.js

エディタでMyMathTest.jsを開いて下記の内容を入力します。

[クリックしてMyMathTest.jsへ移動](#file-mymathtest-js)

ポイントを下記に示します。

1. テスト対象のソースコードをインポートします。
2. main関数では実行するテストケースを列挙します。
3. forループを使ってテストケースを順番に実行します。
4. 例外が発生しなかった場合はOKと出力します。
5. 例外が発生した場合はNGに加えてエラーオブジェクトを出力します。
6. テストケースではまずテスト対象に渡す引数n1、n2、実際の結果actual、期待する結果expectedなどの変数を定義します。
7. 次にassert.strictEqual関数を呼び出します。



## 動作確認

ターミナルで下記のコマンドを実行してaddTestが成功してalwaysFailTestが失敗することを確認します。

```shell
node MyMathTest.js
```

上記のコマンドの実行結果を下記に示します。

```
OK addTest
NG alwaysFailTest
AssertionError [ERR_ASSERTION]: alwaysFail
    at alwaysFailTest (/Users/susukida/workspace/tech-blog/articles/nodejs-test-unit/MyMathTest.js:39:10)
    at main (/Users/susukida/workspace/tech-blog/articles/nodejs-test-unit/MyMathTest.js:17:15) {
  generatedMessage: false,
  code: 'ERR_ASSERTION',
  actual: undefined,
  expected: undefined,
  operator: 'fail'
}
```



## おわりに

Node.jsで単体テストといえば [Mocha](https://mochajs.org/) ですが、itでテストケースのやっていることを英語で説明しようとしては自分の英語力の乏しさを痛感するのでなんとなく疎遠になり、この記事で紹介したようなassertを使った自己流のテストコードを書くようになりました。そろそろ自己流をやめて日本語で書くか関数名みたいに書くかしてMochaに移行しようと思います。

単体テストではテスト対象にデータベースにアクセスするなどの外部依存が含まれていると厄介ですが、そのような場合は関数的な部分（入力のみに従って出力が決定する部分）だけを切り出して単体テストを行い、残りはAPIテストやUIテストに任せる方針にしています。理想をいえば単体テストしやすいソースコードを書ければ良いのですが今の私のレベルでは読みやすさやメンテナンスしやすさを犠牲にしてしまうため、背伸びせずに自分の能力に見合うやり方から逸脱しないように気をつけたいと思います。

私はWebアプリの開発で単体テスト／APIテスト／UIテストの3つを基本にしていますが、どれも自己流で体系的にテストを学んだことがないのでおすすめの書籍やWebサイトなどがありましたらご指導のコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！



## 関連記事

- [Node.jsでfetchを使ってAPIテストを行う方法](https://gist.github.com/tatsuyasusukida/c21445d4a36b07013d79b2baa54e6d61)
- [Node.jsでPuppeteerを使ってWebアプリのUIテストを自動化する方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-test-e2e-puppeteer)
