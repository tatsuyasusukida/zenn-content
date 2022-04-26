---
title: "Node.jsでシリアル通信などの待ち行列を実装する方法"
emoji: "🧘"
type: "tech"
topics: ["nodejs", "serialport"]
published: true
---



# この記事について

この記事では前のリクエストが完了するまで後のリクエストが待機する待ち行列のしくみをNode.jsで実装する方法を紹介します。待ち行列のしくみはシリアル通信のアプリケーションを開発する時など、リクエストを1つずつ順番に処理していく必要がある場合にとりわけ有用です。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-serialport-queue
cd nodejs-serialport-queue
touch Deferred.js RequestQueue.js main.js
```

エディタでDeferred.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/37d380c69e9effa8b6a0e5bed8dce1fc?file=Deferred.js)

ポイントを下記に示します。Deferredについてはazuさんの [JavaScript Promiseの本](https://azu.github.io/promises-book/#deferred-and-promise) の説明がとてもわかりやすいです。

1. コンストラクターではPromiseを生成してresolveとrejectに外部からアクセスできるようにしています。
2. resolveメソッドでは `this._resolve` を呼び出して値を送信しています。
3. rejectメソッドでは `this._reject` を呼び出してエラーを通知しています。

続いてエディタでRequestQueue.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/37d380c69e9effa8b6a0e5bed8dce1fc?file=RequestQueue.js)

ポイントを下記に示します。

1. enqueueメソッドではリクエストを作成して待ち行列に追加しています。
2. リクエストの件数が1の時にprocessメソッドを呼び出しています。
3. リクエストの完了を待機しています。
4. processメソッドではリクエストの件数が1以上の時にループを実行しています。
5. ループの最初では先頭のリクエストを取り出しています。
6. リクエストの関数fnを実行して結果をDeferredのPromiseに送信しています。
7. 完了時に先頭のリクエスト＝現在処理中のリクエストを待ち行列から削除しています。
8. エラーが発生した時にDeferredのPromiseにエラーを通知しています。
9. エラー発生時も先頭のリクエスト＝現在処理中のリクエストを待ち行列から削除しています。

続いてエディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/37d380c69e9effa8b6a0e5bed8dce1fc?file=main.js)

ポイントを下記に示します。

1. main関数の最初ではリクエストの待ち行列を作成しています。
2. リクエスト1を作成して待ち行列に追加しています。
3. リクエスト2を作成して待ち行列に追加しています。
4. リクエスト3を作成して待ち行列に追加しています。
5. すべてのリクエストが待ち行列に追加されたことを出力しています。
6. promise1の完了を待機しています。
7. promise2のエラー発生を待機しています。
8. promise3の完了を待機しています。
9. 各リクエストの結果を出力しています。



# 動作確認

下記のコマンドを実行して出力を確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
Request 1 start
All requests are enqueued
Request 1 end
Request 2 start
Request 2 end
Request 3 start
Request 3 end
promise1: resolve -> one
promise2: reject -> Error: two
promise3: resolve -> three
```

ポイントを下記に示します。

1. Request1が完了するまでRequest2は待機しています。
2. Request2でエラーが発生するまでRequest3は待機しています。
3. Request 1 start は待ち行列に追加した時に出力されるので All requests are enqueued よりも先に表示されています
4. すべてのリクエストが完了した時に promise1: resolve -> one などの結果が出力されています。



# おわりに

紹介しておいて恐縮なのですが紹介した方法がエッジケースでもしっかりと動作するのかについては正直に申し上げて自分でも不安があります。より堅牢な作りにするのであればErlangのようなメッセージパッシングのモデルを参考にして実装した方が良いのではないかと考えています。もし良い方法をご存知の方がいらっしゃいましたらお気軽にコメントを頂ければ幸いです。最後まで読んでいただきありがとうございました。



# 関連記事

- [Node SerialPortでテスト用のモックを作成する方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-serialport-mock)
