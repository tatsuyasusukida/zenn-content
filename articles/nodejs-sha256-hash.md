---
title: "Node.jsでSHA-256のハッシュ値を計算する2通りの方法"
emoji: "#️⃣"
type: "tech"
topics: ["nodejs"]
published: true
---



# この記事について

この記事ではNode.jsの標準ライブラリであるcryptoを使ってSHA-256のハッシュ値を計算する2つの方法を紹介します。一方の方法は同期／非ストリーミング版であり、もう一方の方法は非同期／ストリーミング版です。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/194d062a1f75b9f5cd4636c780d04bb5) からダウンロード可能です。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-sha256-hash
cd nodejs-sha256-hash
touch main.js
```

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/194d062a1f75b9f5cd4636c780d04bb5?file=main.js)

ポイントを下記に示します。

1. sha256hashSyncはSHA-256のハッシュ値を計算する同期／非ストリーミング版の関数です。サイズの小さいデータに向いています。
2. sha256hashAsyncはSHA-256のハッシュ値を計算する非同期／ストリーミング版の関数です。サイズの大きいデータに向いています。



# 動作確認

ターミナルで下記のコマンドを実行して事前にmain.jsのハッシュ値を確認します。

```shell
shasum -a 256 main.js
```

実行結果を下記に示します。

```
8d29af320cd0487c99430827f89798b3f785cb0de4e8261be42a33d7478a19c8  main.js
```

続いてターミナルで下記のコマンドを実行して出力される2つのハッシュ値が先に確認したハッシュ値と一致することを確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
{
  digestSync: '8d29af320cd0487c99430827f89798b3f785cb0de4e8261be42a33d7478a19c8',
  digestAsync: '8d29af320cd0487c99430827f89798b3f785cb0de4e8261be42a33d7478a19c8'
}
```



# 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [Crypto | Node.js v18.0.0 Documentation](https://nodejs.org/api/crypto.html)
- [Obtaining the hash of a file using the stream capabilities of crypto module - Stack Overflow](https://stackoverflow.com/questions/18658612/obtaining-the-hash-of-a-file-using-the-stream-capabilities-of-crypto-module-ie)



# おわりに

Google Cloud Storageなどのストレージにファイルを保存する時にファイル名にハッシュ値を含めることでキャッシュの無効化を不要にしたい場合など、アプリ開発においてハッシュ値の計算が必要になるケースは少なくありません。この記事で紹介したようにNode.jsでは同期／非ストリーミング版と、非同期／ストリーミング版の少なくとも2通りの方法でSHA-256のハッシュ値を計算することができます。後者の非同期／ストリーミング版の方がサイズの大きなデータを扱えるので適用範囲が広いですが、前者の同期／非ストリーミング版の方がシンプルなので扱うデータのサイズが小さい場合は前者の方法がおすすめです。

少し話は変わりますがNode.jsの標準ライブラリのドキュメントをときどき眺めていると新しい機能に気付くことがあります。一例としてv14ではfs/promisesのAPIが利用できるようになりましたが、全然知らなかったのでしばらくの間は毎回npmで [fs-extra](https://www.npmjs.com/package/fs-extra) をインストールして使っていました。この記事をお読みになった方の中で似たような経験をお持ちの方がいましたらお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [Node.jsで非同期処理を扱う6通りの方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-async-await)
- [Node.jsでシリアル通信などの待ち行列を実装する方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-serialport-queue)
