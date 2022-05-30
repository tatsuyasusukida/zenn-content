---
title: "ブラウザで音声を収録する方法【MediaStream Recording API版】"
emoji: "🎤"
type: "tech"
topics: ["javascript", "recording"]
published: true
---

# この記事について

この記事ではJavaScriptで [MediaStream Recording API](https://developer.mozilla.org/docs/Web/API/MediaStream_Recording_API) を利用して音声を収録する方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. HTMLページのコーディング
2. 動作確認



# HTMLページのコーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-recorder-audio
cd column-recorder-audio
touch index.html
touch main.js
```

エディタでindex.htmlを開き、下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ec7f682629171b420726099451992c02?file=index.html)

続いてエディタでmain.jsを開き、下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ec7f682629171b420726099451992c02?file=main.js)

ポイントを下記に示します。

1. HTMLページの要素を取得しています。
2. マイクから音声を取り込むためのメディアストリームを取得しています。
3. audio/webm形式での録音が可能かどうかを確認しています。
4. メディアレコーダーを作成しています。
5. ユーザーが記録を開始するボタンをクリックした時のイベントハンドラを設定しています。
6. ユーザーが記録を終了するボタンをクリックした時のイベントハンドラを設定しています。
7. メディアレコーダーのデータ利用可能になった時のイベントハンドラを設定しています。



# 動作確認

下記のコマンドを実行するなどしてWebブラウザでindex.htmlを開きます。

```shell
open index.html
```

マイクへのアクセスの許可を求められるので「許可」ボタンをクリックします。

![Webブラウザのアドレスバーの下に「このファイルが次の許可を求めています」「マイクを使用する」のメッセージに加え、ブロックボタンと許可ボタンが表示されています。](https://storage.googleapis.com/zenn-user-upload/edf5096ffe10-20220419.png)

「許可」ボタンをクリックしてから「記録を開始する」ボタンをクリックすると音声の記録が開始します。

![記録を開始するボタンをクリックした直後の様子です。記録を開始するボタンが無効になっており、記録を終了するボタンが有効になっています。](https://storage.googleapis.com/zenn-user-upload/7b88a9a73fd1-20220419.png)

「記録を終了するボタン」ボタンをクリックすると音声の記録が終了します。

![記録を終了するボタンをクリックした直後の様子です。記録を終了するボタンが無効になっており、記録を開始するボタンが有効になっています。](https://storage.googleapis.com/zenn-user-upload/8ccd1eb5c9fc-20220419.png)

プレイヤーの再生ボタンをクリックすると記録した音声が再生されます。

![プレイヤーの再生ボタンをクリックした直後の様子です。プレイヤーの再生ボタンが一時停止ボタンに変化しています。](https://storage.googleapis.com/zenn-user-upload/d0c1e8b2b954-20220419.png)



# おわりに

音声を記録するには [Web Audio API](https://developer.mozilla.org/docs/Web/API/Web_Audio_API) を利用する方法もあり、Web Audio APIを利用する場合はやや複雑になります。
Web Audio APIの使い方については [別の記事](https://zenn.dev/tatsuyasusukida/articles/097321c14ec6f5) で紹介していますので興味がありましたら併せてご覧いただければ幸いです。
最後までお読みいただきありがとうございました！
