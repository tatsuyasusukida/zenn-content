---
title: "Node.jsでBase64URLエンコード／デコードを行う方法【動画版あり】"
emoji: "⚾️"
type: "tech"
topics: ["nodejs"]
published: true
---

## この記事について

この記事ではNode.jsで [Base64URL](https://tools.ietf.org/html/rfc4648#section-5) のエンコードとデコードを行う方法について紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/ce71456081748242a0bd4cbfcfe44eb7#file-main-js)
- [動画版](https://www.youtube.com/watch?v=gpqohLJw3iE)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/ce71456081748242a0bd4cbfcfe44eb7)

https://www.youtube.com/watch?v=gpqohLJw3iE



## おおまかな手順

おおまか手順を下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-base64url-encode
cd nodejs-base64url-encode
npm init -y
touch main.js
```



## コーディング

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ce71456081748242a0bd4cbfcfe44eb7?file=main.js)

ポイントを下記に示します。

1. encodeBase64Url関数では+を-に置換し、/ を _ に置換し、= を削除します。
2. decodeBase64Url関数ではまず-を+に置換し、_ を / に置換します。次にBuffer.from関数を使ってBase64文字列をBufferに変換します。



## 動作確認

ターミナルで下記のコマンドを実行してoriginalとdecodedが一致することを確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
{ original: 'ま', encoded: '44G-', decoded: 'ま' }
```

念のためBase64 Guruの [Base64URL Decode](https://base64.guru/standards/base64url/decode) を使って確認します。



## おわりに

Base64URLエンコードはエスケープすることなくURLやファイル名に含めることができるのでランダムなバイト列を短めの文字列に変換したい時に便利です。身近なところではOAuth 2.0の [Authorization Code Grant with PKCE](https://tools.ietf.org/html/rfc7636) などで使われています。この記事では勉強のために自前で実装しましたが [base64url](https://www.npmjs.com/package/base64url) のようなnpmパッケージもあるのでBase64URLエンコードを実装する気力も残っていない時は活用させてもらおうと思います。

Hex（16進数）エンコード（例："ま"の場合はe381be）もBase64URLと同様にURLやファイル名に含めることができますがBase64URLよりも長くなってしまします。しかしながら、長いから悪いという訳ではなくHexの方が1バイトに2文字が対応していることからわかりやすかったり、なんか計算機っぽい感じでカッコよくてテンションが上がったりするなど様々なメリットがあります。GitのコミットやSHA256のチェックサムなどハッシュ関係はHexで表示することが多いように思います。ただし、BitcoinのアドレスなどもハッシュですがこちらはBase58なのでハッシュは必ずしもHexという訳では無さそうです。

簡単なテーマだったのでコーディングはすぐに終わりましたがBase64URLエンコードすると"-"が含まれる"ま"を探すのに苦労したので予想外に時間がかかってしまいました。この記事をお読みの方でBase64をテストするのにナイスなテスト文字列などをご存知でしたらご指導のコメントをいただければ幸いです。最後までお読みいただきありがとうございました！
