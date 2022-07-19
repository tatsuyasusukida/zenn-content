---
title: "JPEG画像のファイルサイズを一括で圧縮する"
emoji: "🗜"
type: "tech"
topics: ["docker", "php", "cakephp"]
published: true
---

## この記事について

この記事では指定ディレクトリ内（サブディレクトリを含む）のJPEG画像のファイルサイズを一括で圧縮する方法について紹介します。画像処理にはNode.jsの[sharp](https://www.npmjs.com/package/sharp)を使います。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/928d7a6f655d07f49551e53dd3861da8#file-compress-image-mjs)




## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir compress-jpeg-image
cd compress-jpeg-image
npm init -y
npm install sharp
touch compress-images.mjs main.mjs
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### compress-images.mjs

@[gist](https://gist.github.com/tatsuyasusukida/928d7a6f655d07f49551e53dd3861da8?file=compress-images.mjs)

### main.mjs

@[gist](https://gist.github.com/tatsuyasusukida/928d7a6f655d07f49551e53dd3861da8?file=main.mjs)



## 動作確認

ターミナルで下記のコマンドを実行してオリジナル画像のディレクトリを作成します。

```shell
mkdir img-original
```

オリジナル画像を用意します。今回は[JaimeReimer](https://www.pexels.com/ja-jp/@jaime-reimer-1376930/)さんの[モレーン湖の美しい景色](https://www.pexels.com/ja-jp/photo/2662116/)を使用します。

![](/images/articles/compress-jpeg-image/img-check-01.jpg)

下記のコマンドを実行して画像の圧縮を開始します。

```shell
node main.mjs
```

圧縮後の画像はimgディレクトリに出力されます。

![](/images/articles/compress-jpeg-image/img-check-02.jpg)

画像のファイルサイズが157KBから124KBバイトまで圧縮されました。圧縮率は124KB / 157KB = 79%です。



## おわりに

画像の圧縮には [TinyPNG](https://tinypng.com/) などを利用することもできますが、一度に処理できる画像が20件までなどの制限があります。大量の画像を圧縮する必要がある場面などでこの記事で紹介した方法がお役に立てば幸いです。
