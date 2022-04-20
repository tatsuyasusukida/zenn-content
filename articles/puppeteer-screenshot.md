---
title: "Puppeteerでスクリーンショットを撮影する方法"
emoji: "📷"
type: "tech"
topics: ["nodejs", "puppeteer"]
published: true
---

# この記事について

この記事では [Puppeteer](https://pptr.dev/) を使ってWebページのスクリーンショットを撮影する方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-puppeteer-screenshot
cd column-puppeteer-screenshot
npm init -y
npm install --save-dev puppeteer
touch main.js
```

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/30e8aa97c8b59463e8f3cb25fa1cab8b?file=main.js)

ポイントを下記に示します。

1. ブラウザを起動してします。
2. 新しいページ（タブ）を生成してします。
3. 撮影対象のページと出力先のファイルを定義しています。
4. 画像の横幅や高さを指定しています。deviceScaleFactorを設定することで画像の解像度を調整することができます。
5. スクリーンショットを出力するディレクトリを作成しています。
6. ページへ移動しています。
7. スクリーンショットを保存しています。
8. ブラウザを終了しています。



# 動作確認

下記のコマンドを実行してスクリーンショットを撮影します。

```shell
node main.js
```

コマンドの実行が完了するとimgディレクトリとzenn.pngが生成されます。

![撮影されたスクリーンショットです。zenn.devのトップページです。Zennのロゴ、検索フォーム、Log inボタンなどが表示されています。](/images/articles/puppeteer-screenshot/check-01.png)



# おわりに

私は要件定義書に掲載する画面例のスクリーンショット撮影を自動化するために今回紹介した方法を使ってします。
数件のスクリーンショットであれば手動で撮影するのもそれほど苦ではありませんが（それでも正直面倒ですが）何十件もスクリーンショットを撮影するとなると骨が折れますし、変更が発生すると管理も煩雑です。

この記事で何百件ものスクリーンショットを手動で撮影することを指示されて困っている方のお役に立てば幸いです。
最後まで読んでいただきありがとうございました！
