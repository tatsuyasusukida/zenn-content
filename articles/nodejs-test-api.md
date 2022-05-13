---
title: "Node.jsでfetchを使ってAPIテストを行う方法【動画版あり】"
emoji: "✌️"
type: "tech"
topics: ["nodejs", "test"]
published: true
---


## この記事について

この記事ではNode.jsでfetchを使ってAPIテストを行う方法について紹介します。なお、Node.jsでfetchを使えるようにするために [node-fetch](https://www.npmjs.com/package/node-fetch) のnpmパッケージをインストールします。

- [ソースコード](https://gist.github.com/tatsuyasusukida/c21445d4a36b07013d79b2baa54e6d61#file-api-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/c21445d4a36b07013d79b2baa54e6d61)
- [動画版](https://www.youtube.com/watch?v=CaDI1r5UKWA)

https://www.youtube.com/watch?v=CaDI1r5UKWA



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
mkdir nodejs-test-api
cd nodejs-test-api
npm init -y
npm install --save express
npm install --save-dev node-fetch@2
touch api.js
```

node-fetchをrequireを使ってインポートする場合はバージョン2である必要があるので@2を末尾に付加してインストールします。



## ソースコードの作成

### api.js

エディタでapi.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/c21445d4a36b07013d79b2baa54e6d61?file=api.js)



## テストコードの作成準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
touch api.test.js
```



## テストコードの作成

### api.test.js

エディタでapi.test.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/c21445d4a36b07013d79b2baa54e6d61?file=api.test.js)

ポイントを下記に示します。

1. node-fetchをfetchとしてインポートします。
2. fetch関数を呼び出してAPIにアクセスします。
3. assert.strictEqualを使ってステータスコードなどの実際の値と期待する値が一致することをチェックします。
4. assert.deepStrictEqualを使うとオブジェクトの一致を丸ごとチェックできるので便利です。



## 動作確認

ターミナルで下記のコマンドを実行してAPIを起動します。

```shell
node api.js
```

続いて新規のターミナルを起動してから下記のコマンドを実行してAPIテストを実施します。

```shell
node api.test.js
```

上記のコマンドの実行結果が下記と一致することを確認します。

```
{ actual: { ok: true }, expected: { ok: true } }
OK getApiStatusTest
```



## おわりに

HTTPリクエストを送信するライブラリとしては [axios](https://axios-http.com/) が有名ですが、fetchはフロントエンドのJavaScriptに標準搭載なので覚えることを少なくするためにnode-fetchを使ってNode.jsでもfetchを使えるようにしています。

単体テストやUIテストと比較してAPIテストはデータベースアクセスなども扱うことができる上にテストコードが書きやすいので他の2つに比べて重宝しています。[Sequelize](https://sequelize.org/) を使っている場合はsyncメソッド＋テストデータのinsertを呼び出して必要なタイミングでデータベースを初期化できるようにすると便利です。

WebアプリによってはAPIへのアクセスに認証や認可が必要となりますがOAuth 2.0を使っていてClient Credentials Grantを利用できる場合はJWTなどのアクセストークンを [Auth0](https://auth0.com/) などの認可エンドポイントからもらう一手間が増えるだけなのでそれほど面倒ではありません。一方で認証にcookieを使っている場合はちょっと面倒です。私はWebブラウザで手動でログインしてから開発者ツールを使ってcookieを探してコピーし、テストコードを実行する時に環境変数などを使って渡しています。このやり方は手間ですし明らかにセキュアじゃない感じがするので何か良い方法をご存知の方がいましたらご指導のコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！



## 関連記事

- [Node.jsで標準ライブラリだけを使って単体テストを行う方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-test-unit)
- [Node.jsでPuppeteerを使ってWebアプリのUIテストを自動化する方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-test-e2e-puppeteer)
