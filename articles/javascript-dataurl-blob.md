---
title: "JavaScriptでdata URLとblobを相互に変換する方法"
emoji: "♾"
type: "tech"
topics: ["javascript"]
published: true
---


# この記事について

この記事ではブラウザ側のJavaScriptでdata URLとblobを相互に変換する方法について紹介します。
ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/cc00987113c80a916f2204e452352a8f) からダウンロード可能であり、結果については [デモ](https://gist.githack.com/tatsuyasusukida/cc00987113c80a916f2204e452352a8f/raw/index.html) からご確認可能です。



# おおまかな手順

おおまか手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-big-number
cd nodejs-big-number
npm init -y
npm install --save big.js
touch index.html main.js
```

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/cc00987113c80a916f2204e452352a8f?file=index.html)

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/cc00987113c80a916f2204e452352a8f?file=main.js)

ポイントを下記に示します。

1. convertBlob関数はblobをdata URLに変換する関数です。FileReaderはBlob型やFile型のオブジェクトをdata URLやArrayBufferとして読み込むためのクラスです。FileReaderにはabort（読み込み中止）、error（エラー発生）、load（読み込み完了）の3つのイベントがあるので、それぞれのイベントハンドラと登録／解除のためのsubscribe関数とunsubscribe関数を定義しています。それぞれのイベントの発生時にはunsubscribe関数を呼び出してイベントハンドラの解除しています。
2. convertDataUrl関数はdata URLをblobに変換する関数です。fetch関数を使ってResponseオブジェクトに変換した後にblobメソッドを呼び出してblobに変換しています。いずれもasync関数なのでawaitを2つ必要とします。



# 動作確認

ターミナルで下記のコマンドを実行してWebサーバーを起動します。

```shell
npx http-server -c-1
```

ブラウザで http://localhost:8080/ にアクセスします。

![ブラウザで http://localhost:8080/ にアクセスした様子です。Webページの見出しはJavaScriptでdata URLとblobを相互に変換する方法であり、本文は下記のリンクをクリックして実行結果をご確認ください、です。Webページにはオリジナルのblobデータ、blobからdataURLへの変換結果、dataURLからblobへの変換結果の3つのリンクが含まれています。](/images/articles/javascript-dataurl-blob/img-check-01.png)

Webページには下記のリンク3点が含まれています。

- オリジナルのblobデータ
- blobからdataURLへの変換結果
- dataURLからblobへの変換結果

上記のリンクをクリックして内容が下記であることを確認します。

```
Blob
```


# 参考にしたWebページ

この記事を書くために参考にしたWebページを下記に示します。

- [FileReader - Web API | MDN](https://developer.mozilla.org/ja/docs/Web/API/FileReader)



# おわりに

この記事を作成している時に知ったこと2点を下記に示します。

- Google Chromeではdata URLを新しいタブで開けないのでa要素のdownload属性を使うなどして対応する必要がある。
- Google ChromeではWebページをファイルとして開くとfetchする時にエラーが発生するのでnpmのhttp-serverなどを使う必要がある。と思っていたけれど再現しようと思ってもう1回やってみたら大丈夫だった。何を間違えたのだろう...

どうでも良いことですがblobとタイプする時にblogとタイプしてしまうのは私だけでしょうか。blobからdata URLに変換する方法（FileReaderを使う方）は毎回「コード量が多くて面倒」と感じているので何か良い方法をご存知の方がいましたらご指導をお願いできれば幸いです。最後までお読みいただきありがとうございました！
