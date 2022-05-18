---
title: "JavaScriptでSVGをPNGに変換する方法【動画版あり】"
emoji: "🌅"
type: "tech"
topics: ["javascript", "svg"]
published: true
---

## この記事について

この記事ではフロントエンドのJavaScriptでSVGをPNGに変換する方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/1261585e3422da5645a1cbb9cf8813d6#file-index-html)
- [デモ](https://gist.githack.com/tatsuyasusukida/1261585e3422da5645a1cbb9cf8813d6/raw/index.html)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/1261585e3422da5645a1cbb9cf8813d6)
- [動画版](https://www.youtube.com/watch?v=pxhvAr2kFGA)

https://www.youtube.com/watch?v=pxhvAr2kFGA



## おおまかな流れ

おおまかな流れを下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir javascript-svg-png
cd javascript-svg-png
touch index.html main.js
```



## コーディング

### index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/1261585e3422da5645a1cbb9cf8813d6?file=index.html)

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/1261585e3422da5645a1cbb9cf8813d6?file=main.js)



## 動作確認

ブラウザでindex.htmlを開きます。macOSの場合はターミナルで下記のコマンドを実行すると便利です。

```shell
open index.html
```

Webページ内の「出力されるPNG画像」セクションにPNG画像が表示されていることを確認します。

![ブラウザでindex.htmlを開いた様子です。Webページの見出しは「JavaScriptでSVGをPNGに変換する方法」であり、「入力されるSVG画像」セクションと「出力されるPNG画像」セクションには同じ灰色の円の画像がそれぞれ含まれています。](/images/articles/javascript-svg-png/img-check-01.png)



## おわりに

サンプルのソースコードにはSVGのテキストをbase64に変換する下記の1文が含まれています。

```js
const svgDataBase64 = btoa(unescape(encodeURIComponent(svgData)))
```

どのように過程を経てbase64に変換されるのかが気になったので調べてみました。下記は変換前のSVGのテキストです。

```
<svg xmlns="http://www.w3.org/2000/svg" width="240" height="240" id="input">
  <circle cx="120" cy="120" r="90" fill="#888888"/>
</svg>
```

続いて下記は `encodeURIComponent(svgData)` の実行結果です。

```
%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%22240%22%20height%3D%22240%22%20id%3D%22input%22%3E%0A%20%20%20%20%20%20%20%20%3Ccircle%20cx%3D%22120%22%20cy%3D%22120%22%20r%3D%2290%22%20fill%3D%22%23888888%22%2F%3E%0A%20%20%20%20%20%20%3C%2Fsvg%3E
```

続いて下記は `unescape(encodeURIComponent(svgData))` の実行結果です。

```
<svg xmlns="http://www.w3.org/2000/svg" width="240" height="240" id="input">
  <circle cx="120" cy="120" r="90" fill="#888888"/>
</svg>
```

最後に下記は `btoa(unescape(encodeURIComponent(svgData)))` の実行結果です。

```
PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNDAiIGhlaWdodD0iMjQwIiBpZD0iaW5wdXQiPgogICAgICAgIDxjaXJjbGUgY3g9IjEyMCIgY3k9IjEyMCIgcj0iOTAiIGZpbGw9IiM4ODg4ODgiLz4KICAgICAgPC9zdmc+
```

btoaは文字列をBase64に変換する関数ですが、ASCII文字以外を含む文字列を引数として渡すことはできません。そのためencodeURIComponent関数とunescape関数を使って非ASCII文字をASCII文字に変換しています。例えばひらがなの「あ」はUTF-8では0xE3 0x81 0x82と3バイトなので `encodeURIComponent('あ')` の結果は %E3%81%82 になります（ちなみに `encode('あ')` の場合はSyntaxError例外が発生します）。一方、unescape関数は %E3%81%82 を3文字と認識するので `unescape('%E3%81%82')` の結果は 'ã\x81\x82' になります。

JavaScriptの `btoa(unescape(encodeURIComponent(text)))` はNode.jsにおける `Buffer.from(text).toString('base64')` と同じようなものと理解しました。ということは `decodeURIComponent(escape(atob(base64)))` とすればbase64からテキストに変換できるのでしょうか（Node.jsにおける `Buffer.from(base64, 'base64').toString()` と同じようなものなのでしょうか）。下記のコードを実行したところ「あ」と評価されたのでどうやら正しいみたいです。

```js
decodeURIComponent(escape(atob(btoa(unescape(encodeURIComponent('あ'))))))
```

いつも訳もわからずコピペしていただけだったので色々と調べる時間を設けたおかげでまた一つ賢くなることができました。この記事が同じような疑問を持った方のお役に立てば幸いです。最後までお読みいただきありがとうございました！
