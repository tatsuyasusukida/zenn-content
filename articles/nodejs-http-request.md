---
title: "Node.jsで標準ライブラリだけを使ってHTTPリクエストを送信する方法【動画版あり】"
emoji: "📤"
type: "tech"
topics: ["nodejs"]
published: true
---

## この記事について

この記事ではNode.jsの標準ライブラリであるhttpを使い、メソッドやヘッダーなどを指定した上でHTTPリクエストを送信する方法について紹介します。動作確認のために受信したHTTPリクエストの情報を返送するサーバーをExpressを使って作成します。この記事の関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/da9a3376de13e0d59f16a3c231d3c171#file-client-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/da9a3376de13e0d59f16a3c231d3c171)
- [動画版](https://www.youtube.com/watch?v=0nYYbS-dRCs)

https://www.youtube.com/watch?v=0nYYbS-dRCs



## おおまかな手順

おおまか手順を下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-http-request
cd nodejs-http-request
npm init -y
npm install --save express
touch server.js client.js
```



## コーディング

### server.js

エディタでserver.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/da9a3376de13e0d59f16a3c231d3c171?file=server.js)

### client.js

エディタでclient.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/da9a3376de13e0d59f16a3c231d3c171?file=client.js)

ポイントを下記に示します。

1. http.request関数を使ってHTTPリクエストを作成します。
2. HTTPリクエストのresponseイベントに対してPromiseのresolveを登録します。
3. HTTPリクエストのerrorイベントに対してPromiseのrejectを登録します。
4. HTTPリクエストのwriteメソッドを使ってHTTPリクエストボディを書き込みます。
5. HTTPリクエストのendメソッドを使ってHTTPリクエストを完了します。
6. HTTPレスポンスのdata、end、errorの3つのイベントに対してイベントハンドラを登録します。dataイベントではHTTPレスポンスボディの断片chunkをchunksリストの末尾に追加します。
7. endイベントではchunksリストでPromiseのresolveを呼び出します。
8. errorイベントではPromiseのrejectを呼び出します。
9. Buffer.concat関数を呼び出してHTTPレスポンスボディの断片を結合します。



## 動作確認

ターミナルで下記のコマンドを実行してサーバーを起動します。

```shell
node server.js
```

新規のターミナルを起動してから下記のコマンドを実行してHTTPリクエストを送信します。

```shell
node client.js
```

実行結果が下記と一致することを確認します。

```
{
  "method": "POST",
  "url": "/pathname?query=1",
  "query": {
    "query": "1"
  },
  "headers": {
    "content-type": "text/plain",
    "content-length": "7",
    "x-header": "X-Header",
    "host": "localhost:3000",
    "connection": "close"
  },
  "body": "content"
}
```



## おわりに

Node.jsでHTTPリクエストを送信する場合は [node-fetch](https://www.npmjs.com/package/node-fetch) を使っており、標準ライブラリだと40行弱を要する処理がfetchだと数行で終わるので簡単です。OAuth 2.0のアクセストークンを取得するときやAPIテストを実施する時など、Node.jsからHTTPリクエストを送信したい状況は少なくありません。やったことないからよく知らないのですがマイクロサービスアーキテクチャでサービス間の通信にもHTTPリクエストを使うのでしょうか？（さらによく知らないけどgRPCとか使うのでしょうか？）

どういう状況かは想像いたしかねますがNode.jsでnode-fetchなどのnpmパッケージを使わずにHTTPリクエストを送信したい方にとってこの記事が役に立てば幸いです。最後までお読みいただきありがとうございました！
