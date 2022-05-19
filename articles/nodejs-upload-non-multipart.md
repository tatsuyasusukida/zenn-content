---
title: "Node.jsでmultipart/form-dataを使わずにファイルをアップロードする方法【動画版あり】"
emoji: "⬆️"
type: "tech"
topics: ["nodejs"]
published: true
---

## この記事について

この動画ではNode.jsでmultipart/form-dataを使わずにリクエストボディに直接blobオブジェクトを指定してファイルをアップロードする方法を紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/8937f63d1baa1833910a7d2b6200df3d#file-client-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/8937f63d1baa1833910a7d2b6200df3d)
- [動画版](https://www.youtube.com/watch?v=PkDJphdtQoo)

https://www.youtube.com/watch?v=PkDJphdtQoo



## おおまかな流れ

おおまかな流れを下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-upload-non-multipart
cd nodejs-upload-non-multipart
npm init -y
touch client.js index.html server.js
```



## コーディング

### index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/8937f63d1baa1833910a7d2b6200df3d?file=index.html)

### client.js

エディタでclient.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/8937f63d1baa1833910a7d2b6200df3d?file=client.js)

ポイントを下記に示します。

1. ファイル名などのパラメーターを送信したい場合、クエリストリングを使用します。
2. fetch関数のbodyにfileを指定して呼び出します。

### server.js

エディタでserver.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/8937f63d1baa1833910a7d2b6200df3d?file=server.js)

ポイントを下記に示します。

1. リクエストボディーをバッファーとして取得します。
2. fsPromises.writeFile関数を使用してファイルを書き込みます。ファイルを書き込む前にfsPromises.mkdir関数を使用してディレクトリを作成します。

The points are as follows:

1. Get the request body as a buffer.
2. Write the file using the fsPromises.writeFile function. Create a directory using the fsPromises.mkdir function before writing the file.



## 動作確認

下記のコマンドを実行してサーバーを起動します。

```shell
node server.js
```

ブラウザで http://localhost:3000/ にアクセスします。

ファイルを選択してから送信ボタンをクリックします。

選択されたファイルがアップロードされることを確認します。



## おわりに

この記事で紹介した方法の利点は、multipart/form-dataを使用する方法よりも（実装によりけりですが）メモリの消費量を少なくできることだと考えています。

サーバーが受信したファイルをGoogle Cloud StorageやAmazon S3などのクラウドストレージに送信する場合、ファイルをローカルストレージに書き込む必要はありません。アップロードするファイルが大きい場合は、リクエストの本文をバッファとして取得するのではなく、fs.createWriteStream関数を使用することがおすすめです。

ご不明な点がございましたら、お気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！
