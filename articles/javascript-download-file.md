---
title: "JavaScriptでテキストファイルなどをダウンロードする方法【動画版あり】"
emoji: "🚎"
type: "tech"
topics: ["nodejs", "test"]
published: true
---


## この記事について

この記事ではフロントエンド（ブラウザ側）のJavaScriptでテキストファイルなどをダウンロードする方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/e7622e783f71933b54193b7affbb3de1#file-index-html)
- [デモ](https://gist.githack.com/tatsuyasusukida/e7622e783f71933b54193b7affbb3de1/raw/index.html)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/e7622e783f71933b54193b7affbb3de1)
- [動画版](https://www.youtube.com/watch?v=4btdYFrPHx0)

https://www.youtube.com/watch?v=4btdYFrPHx0



## おおまかな流れ

おおまかな流れを下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir javascript-download-file
cd javascript-download-file
touch index.html main.js
```



## コーディング

### index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/e7622e783f71933b54193b7affbb3de1?file=index.html)

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/e7622e783f71933b54193b7affbb3de1?file=main.js)

ポイントを下記に示します。

1. ダウンロード対象のデータのblobを作成した後、URL.createObjectURL関数を呼び出してオブジェクトURLを作成します。
2. document.createElementメソッドを呼び出してanchor（aタグ）を動的に生成し、anchor.setAttributeメソッドを使ってhref属性にオブジェクトURLを設定します。また、download属性にダウンロード時のファイル名を設定します。
3. MouseEventのインスタンスを作成した後、anchor.dispatchEventメソッドを呼び出して擬似的にanchorをクリックします。



## 動作確認

ブラウザでindex.htmlを開きます。macOSの場合は下記のコマンドが便利です。

```shell
open index.html
```

Webページ内の「ダウンロード」ボタンをクリックするとファイルのダウンロードが開始することを確認します。



## 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [MouseEvent - Web API | MDN](https://developer.mozilla.org/ja/docs/Web/API/MouseEvent)



## おわりに

この記事で紹介した方法はmacOS Monterey 12.0.1の下記のブラウザで動作を確認しました。

- Google Chrome 101.0.4951.54
- Firefox 100.0
- Safari 15.1

同じような記事については [phi](https://zenn.dev/phi) さんが [JavaScript Tips - 動的に入力した内容のファイルをダウンロードする方法](https://zenn.dev/phi/articles/javascript-donwload-file) の記事をZennに投稿しています。

こちらの記事との違いについては、phiさんの記事で紹介している方法ではHTML中のaタグのhref属性にオブジェクトURLを設定しているのに対し、この記事で紹介している方法ではJavaScriptでaタグを動的に生成している点です。アプローチは異なりますが得られる結果は同様です。phiさんの記事で紹介している方法の方が簡潔でコード量も少ないです。一方、この記事で紹介している方法はコード量が多いですが、aタグだけではなくbuttonタグなどにも使うことができます。

MouseEventはaタグやbuttonタグのclickイベントでしか生成することができません... と思ってwindowのloadイベントでテストしてみたら普通にできてしまいました笑 前はできなかったような気がするのですが何かと勘違いしているのでしょうか？詳しい方がいらっしゃったらご指導のコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！
