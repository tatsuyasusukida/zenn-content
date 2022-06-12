---
title: "JavaScriptでMediaStream Recording APIを使って音声を収録する方法【デモ+動画あり】"
emoji: "🎤"
type: "tech"
topics: ["javascript", "recording"]
published: true
---

## この記事について

この記事ではJavaScriptで [MediaStream Recording API](https://developer.mozilla.org/docs/Web/API/MediaStream_Recording_API) を利用して音声を収録する方法について紹介します。関連リソースを下記に示します。

- [デモ](https://gist.githack.com/tatsuyasusukida/ec7f682629171b420726099451992c02/raw/index.html)
- [デモ動画](https://www.youtube.com/watch?v=NL7jgSWerGU)
- [ソースコード](https://gist.github.com/tatsuyasusukida/ec7f682629171b420726099451992c02#file-main-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/ec7f682629171b420726099451992c02)

https://www.youtube.com/watch?v=NL7jgSWerGU



## おおまかな手順

おおまかな手順を下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-recorder-audio
cd column-recorder-audio
touch index.html
touch main.js
```



## コーディング

### index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ec7f682629171b420726099451992c02?file=index.html)

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ec7f682629171b420726099451992c02?file=main.js)

ポイントを下記に示します。

1. マイクから音声を取り込むためのメディアストリームを取得します
2. audio/webm形式での録音が可能かどうかを確認します。
3. メディアレコーダーのインスタンスを作成します。
4. メディアレコーダーのstartメソッドを呼び出して収録を開始します。
5. メディアレコーダーのstopメソッドを呼び出して収録を停止します。
6. メディアレコーダーのdataavailableイベントにイベントハンドラを追加して収録した音声データを取得します。



## 動作確認

下記のコマンドを実行するなどしてWebブラウザでindex.htmlを開きます。

```shell
open index.html
```

マイクへのアクセスの許可を求められるので「許可」ボタンをクリックします。

![Webブラウザのアドレスバーの下に「このファイルが次の許可を求めています」「マイクを使用する」のメッセージに加え、ブロックボタンと許可ボタンが表示されています。](/images/articles/javascript-media-audio/img-check-01.png)

「許可」ボタンをクリックしてから「記録を開始する」ボタンをクリックすると音声の記録が開始します。

![記録を開始するボタンをクリックした直後の様子です。記録を開始するボタンが無効になっており、記録を終了するボタンが有効になっています。](/images/articles/javascript-media-audio/img-check-02.png)

「記録を終了するボタン」ボタンをクリックすると音声の記録が終了します。

![記録を終了するボタンをクリックした直後の様子です。記録を終了するボタンが無効になっており、記録を開始するボタンが有効になっています。](/images/articles/javascript-media-audio/img-check-03.png)

プレイヤーの再生ボタンをクリックすると記録した音声が再生されます。

![プレイヤーの再生ボタンをクリックした直後の様子です。プレイヤーの再生ボタンが一時停止ボタンに変化しています。](/images/articles/javascript-media-audio/img-check-04.png)



## おわりに

音声を記録するには [Web Audio API](https://developer.mozilla.org/docs/Web/API/Web_Audio_API) を利用する方法もありますが、この記事で紹介したMediaStream Recordingを利用する方法よりもやや複雑です。Web Audio APIの使い方については [JavaScriptでWeb Audio APIを使って音声を収録する方法の記事](https://zenn.dev/tatsuyasusukida/articles/097321c14ec6f5) で紹介していますので興味がありましたら併せてご覧いただければ幸いです。最後までお読みいただきありがとうございました！
