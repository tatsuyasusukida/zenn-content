---
title: "Node.jsで非同期処理を扱う6通りの方法"
emoji: "✈️"
type: "tech"
topics: ["nodejs"]
published: true
---

# この記事について

この記事ではNode.jsで非同期処理を扱う下記6通りの方法とそれぞれのメリット／デメリットを紹介します。

1. 同期バージョンの関数を使う
2. コールバック関数を使う
3. ストリームを使う
4. Promiseを使う
5. async/awaitを使う
6. RxJS（Observable）を使う



# 同期バージョンの関数を使う

@[gist](https://gist.github.com/tatsuyasusukida/5f0850d75be41f922ae6c4e74ee86acc?file=case-01-sync.js)

初っ端からタイトルに偽りありという感じが否めないですが(笑)
`fs.readFileSync` と `fs.readFile` のように関数によっては同期バージョンと非同期バージョンの両方が用意されていることがあるので、処理速度がそれほど重要ではない場面などでは同期バージョンを使うことで面倒な非同期処理を避けることができます。

## メリット

コーディングの量が減る、requireされた時に実行できる（[Sequelizeのmodels/index.js](https://github.com/sequelize/cli/blob/main/src/assets/models/index.js) などで活用例を見ることができます）

## デメリット

ブロックが発生するので処理速度が遅くなる。



# コールバック関数を使う

@[gist](https://gist.github.com/tatsuyasusukida/5f0850d75be41f922ae6c4e74ee86acc?file=case-02-callback.js)

関数の引数（多くの場合は最後の引数）としてコールバック関数を指定し、処理が完了した時に関数側から呼び出してもらう方法です。コールバック関数の最初に引数である `err` はエラーの発生を伝えるための引数であり、処理の成功時はnullが渡され、処理の失敗時は `new Error('error message')` のようなエラーオブジェクトが渡されます。

## メリット

Node.jsで初期の頃から使わてきた方法なので、運悪く古いバージョンのNode.jsを使用しなければならない場面でも使うことができる。

## デメリット

コールバック関数の呼び出しが続くとネストが深くなって辛くなる。



# ストリームを使う

@[gist](https://gist.github.com/tatsuyasusukida/5f0850d75be41f922ae6c4e74ee86acc?file=case-03-stream.js)

`fs.createReadStream` 関数を使うなどしてストリームを作成し、dataやerrorイベントなどのハンドラを登録する方法です。endやfinishなどのイベントにハンドラを登録することで完了を検出することができます。

## メリット

とても大きなファイルを扱うような場合でも対応できる（設計次第ですが）。

## デメリット

コーディングの量が増える、使用したい関数がストリームに対応していない場合がある。



# Promiseを使う

@[gist](https://gist.github.com/tatsuyasusukida/5f0850d75be41f922ae6c4e74ee86acc?file=case-04-promise.js)

Promiseについては [JavaScript Promiseの本](https://azu.github.io/promises-book/) という素晴らしいドキュメントがあるので説明を割愛します。

## メリット

`Promise#then` を使うことでコールバック関数の時のようにネストが深くなって辛くなることを防げる。

## デメリット

async/awaitに比べて読みにくい。



# async/awaitを使う

@[gist](https://gist.github.com/tatsuyasusukida/5f0850d75be41f922ae6c4e74ee86acc?file=case-05-async.js)

`fsPromises.readFile` のようにPromiseを返す関数の呼び出しの前に `await` キーワードを書くことによって `Promise#then` の呼び出しを省略することができます。

## メリット

コーディングの量が減る、直感的でわかりやすい。

## デメリット

`await` を利用する関数は `async function` である必要がある。



# RxJS（Observable）を使う

@[gist](https://gist.github.com/tatsuyasusukida/5f0850d75be41f922ae6c4e74ee86acc?file=case-06-rxjs.js)

[RxJS](https://rxjs.dev/)はObservableという概念を中心として非同期処理を宣言的に書くことができるライブラリです。PromiseとObservableは似ていますが下記2つの点が異なっています。

- Promiseでは引数がresolveとrejectの2つであるのに対し、Observableでは引数がnext、error、completeの3つである点。Observableではnextを使ってデータを送り、completeを使って完了を知らせます。
- Promiseが作成と同時に実行されるのに対し、Observableはsubscribeメソッドを呼び出すことによって実行される点。

## メリット

柔軟性が極めて高い、[Operators](https://rxjs.dev/guide/operators) を利用すると例えば「エラーが失敗したら6秒間待ってから再度実行する」ような処理を宣言的に書ける（[RxJSのretryWhenのページ](https://www.learnrxjs.io/learn-rxjs/operators/error_handling/retrywhen#examples)にソースコードのサンプルがあります）。

## デメリット

コーディング量が増える、凄すぎて使いこなせない。



# おわりに

Node.jsでは非同期処理を扱う方法が複数あります。
どの方法を選べば良いかを決める時などにこの記事が参考になれば幸いです。
最後までお読みいただきありがとうございました！
