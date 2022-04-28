---
title: "JavaScriptでpadStartが使えない時の代替手段"
emoji: "🗓"
type: "tech"
topics: ["javascript"]
published: true
---



# この記事について

この記事ではJavaScriptでpadStartが使えない環境でも利用できるsliceを使った代替手段について紹介します。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/935c7d58609240e96bd8960a81b555b7) からダウンロード可能です。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir javascript-pad-start
cd javascript-pad-start
touch main.js
```

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/935c7d58609240e96bd8960a81b555b7?file=main.js)

ポイントを下記に示します。

1. 000を先頭に付加しています。例えばstrが4d2の場合は結果は0004d2になります。
2. sliceは部分文字列を返却する関数であり、1つ目の引数で開始位置を指定します。開始位置が負数の場合は末尾から遡ります。例えばstep1が0004d2の場合にslice(-4)を呼び出すと結果は04d2になります。
3. 16進数であることを示す0xを先頭に付加しています。0x0000を付加してからslice(-4)を呼び出さないようにご注意ください。例えばstrが4d2の場合はstep1が0x00004d2となるのでslice(-4)を呼び出すと結果は04d2になり、意図する結果である0x04d2にはなりません。



# 動作確認

ターミナルで下記のコマンドを実行して期待する結果expectedと実際の結果actualが一致することを確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
{ expected: '0x04d2', actual: '0x04d2' }
```



# 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [String.prototype.padStart - JavaScript | MDN](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String/padStart)



# おわりに

padStartを使う方法とsliceを使う方法を比較すると下記からわかるように入力文字数などにほとんど違いはありません。

- padStartを使う方法: `'0x' + str.padStart(4, '0')`
- sliceを使う方法: `'0x' + ('000' + str).slice(-4)`

したがってsliceを使う方法の方が互換性が高い分だけメリットがあるように思えますが、padStartが利用できないのはIEくらいであり、そのIEも2022年6月にリタイア予定なので直感的にわかりやすいpadStartを使う方法の方だけを今後は知っていれば良いように思います。ただ、いつもpadStartの引数の順序を間違えるんですよね...

皆さんはレガシーブラウザを意識してコーディングをすることはあるでしょうか？もしありましたらお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [Node.jsで非同期処理を扱う6通りの方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-async-await)
- [Node.jsでシリアル通信などの待ち行列を実装する方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-serialport-queue)
