---
title: "FFmpegで写真と音声から動画を作成する方法"
emoji: "🎞"
type: "tech"
topics: ["ffmpeg", "cloudrun"]
published: true
---

# この記事について

この記事では [FFmpeg](https://ffmpeg.org/) を使って写真（連番画像）と音声から動画を作成する方法を紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. FFmpegをインストールする
2. 画像を用意する
3. 音声を用意する
4. コマンドを実行する
5. 動作確認



# FFmpegをインストールする

ブラウザで [FFmpegのダウンロードページ](https://ffmpeg.org/download.html) にアクセスして実行可能ファイルをダウンロードすることができます。

![FFmpegのダウンロードページです。Linux、Windows、macOS向けの実行可能ファイルのダウンロードリンクが含まれています。](/images/articles/ffmpeg-image-audio/install-01.png)

macOSでは下記のコマンドを実行してFFmpegをインストールすることが可能です。

```shell
brew install ffmpeg
```



# 画像を用意する

ターミナルのスクリーンショットを撮影するなどして素敵な画像を何枚か用意します。

![何もないターミナルのスクリーンショットです。](/images/articles/ffmpeg-image-audio/image-01.png)

![ターミナルで echo 1 を実行した直後のスクリーンショットです](/images/articles/ffmpeg-image-audio/image-02.png)

![ターミナルで echo 2 を実行した直後のスクリーンショットです](/images/articles/ffmpeg-image-audio/image-03.png)

![ターミナルで echo 3 を実行した直後のスクリーンショットです](/images/articles/ffmpeg-image-audio/image-04.png)

コマンドを実行する時のために画像のファイル名を下記のように変更します。

- image-01.png
- image-02.png
- image-03.png
- image-04.png




# 音声を用意する

音声は [もっぴーさうんど](https://dova-s.jp/_contents/author/profile060.html) さんが制作した [ほんわかぷっぷー](https://dova-s.jp/bgm/play1858.html) というBGMを利用させていただきました。コマンドを実行する時のために画像のファイル名を下記のように変更します。

- audio.mp3



# コマンドを実行する

`cd` コマンドを使って画像と音声が含まれるディレクトリに移動してから下記のコマンドを実行して動画を生成します。

```shell
  ffmpeg \
    -y \
    -r 0.5 \
    -i image-%02d.png \
    -i audio.mp3 \
    -vcodec libx264 \
    -pix_fmt yuv420p \
    -t 8 \
    video.mp4
```

コマンドライン引数のついての説明を下記に示します。

- -y: 出力ファイルを上書きするためのオプションです。
- -r: 動画のフレームレートをHz単位で指定するオプションです。
- -i: 入力する画像や音声を指定するオプションです。
- -vcodec: ビデオのコーデックを指定するオプションです。
- -pix_fmt: ピクセルフォーマットを指定するオプションです。
- -t: 動画の長さを秒単位で指定するオプションです。
- video.mp4: 出力される動画のファイル名です。



# 動作確認

生成された動画をYouTubeにアップロードしましたので下記に示します。

https://youtu.be/8NKb9xa-kPw



# おわりに

別の記事で紹介している [ブラウザで写真を撮影する方法](https://zenn.dev/articles/84e789bea68b1e/edit) や [ブラウザで音声を収録する方法](https://zenn.dev/articles/e9fc25bcdbd370/edit) とこの記事で紹介した写真と音声から動画を作成する方法を組み合わせることでブラウザで動画を撮影することができます。

ほとんどのブラウザでは [MediaStream Recording API](https://developer.mozilla.org/docs/Web/API/MediaStream_Recording_API) を利用できるのでわざわざ手間をかける必要はありませんが、何らかの事情で利用できない状況では代替手段として重宝します。

ちなみに MediaStream Recording APIを使って [ブラウザで動画を撮影する方法](https://zenn.dev/articles/aa6e061d8b5273/edit) についても別の記事で紹介していますので興味がありましたら併せてご覧いただければ幸いです。
最後まで読んでいただきありがとうございました！