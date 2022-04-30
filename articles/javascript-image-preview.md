---
title: "JavaScriptで画像ファイル選択時にプレビューを表示する方法"
emoji: "🏞"
type: "tech"
topics: ["javascript"]
published: true
---


# この記事について

この記事では `<input type="file">` 要素を使って選択された画像ファイルのプレビューをJavaScriptを使って表示する方法について紹介します。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/980b61ebea1d93000a302d01b5725507) からダウンロード可能であり、結果については [デモ](https://gist.githack.com/tatsuyasusukida/980b61ebea1d93000a302d01b5725507/raw/index.html) からご確認可能です。



# おおまかな手順

おおまか手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir javascript-image-preview
cd javascript-image-preview
touch index.html main.js
```

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/980b61ebea1d93000a302d01b5725507?file=index.html)

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/980b61ebea1d93000a302d01b5725507?file=main.js)

ポイントを下記に示します。

1. input要素のchangeイベントハンドラでは、選択されたファイルのオブジェクトURLを作成してimg要素のsrc属性に設定します。`ESC` キーを押すなどしてファイルの選択をキャンセルすることもあるのでif文を使って選択されたファイルの件数をチェックしています。



# 動作確認

ブラウザでindex.htmlを開きます、macOSの場合はターミナルで下記のコマンドを実行すると便利です。

```shell
open index.html
```

![ブラウザでindex.htmlを開いた様子です。Webページの見出しの内容はJavaScriptで画像ファイル選択時にプレビューを表示する方法であり、本文は画像ファイルを選択してプレビューが表示されることをご確認ください、です。Webページには画像ファイルの選択部が含まれています。](/images/articles/javascript-image-preview/img-check-01.png)

画像ファイルの選択部をクリックして適当な画像ファイルを選択してプレビューが表示されることを確認します。

![画像ファイルを選択した直後の様子です。画像ファイルのプレビューが追加れています。](/images/articles/javascript-image-preview/img-check-02.png)



# おわりに

WebサーバーでContent Security Policyを設定している場合はプレビューが表示されないことがあるのでご注意ください。特にChromeで開発していて本番環境でSafariやiPhoneからアクセスすると動かないことがあるので、その場合はimg-src、connect-src、media-srcなどに `blob:` を追加することをお試しください。また、この記事では扱いませんでしたが動画についても同様の手順でプレビューを表示することができます。画像や動画のプレビューを表示したいという要望は少なくないのでこの記事が実装方法を探している方のお役に立てば幸いです。最後までお読みいただきありがとうございました！
