---
title: "Cloud Run で Buildpacks 使用時に実行イメージをカスタマイズする方法"
emoji: "🔧"
type: "tech"
topics: ["cloudrun", "buildpacks"]
published: true
---

## この記事について

Cloud Run を使っていて実行イメージにちょっとした変更（例：パッケージのインストール）を加えたいけど、それ以外は [Google Cloud の Buildpacks](https://cloud.google.com/docs/buildpacks/overview?hl=ja) によろしくやってもらいたいと思う時があります。下記の公式ドキュメントではまさにそのやり方が紹介されています。

https://cloud.google.com/docs/buildpacks/build-run-image?hl=ja

ドキュメントが用意されているのはありがたいことですが、この記事の作成時点（2023 年 11 月）では書かれている説明の通りにやっても失敗します。色々と試した結果どうにか成功できたので、この記事ではその過程で得られた知見をまとめようと思います。

### Zenn スクラップ

この記事は下記のスクラップをベースにしています。

https://zenn.dev/tatsuyasusukida/scraps/8cbce7c9d5b90c

不足している情報などがありましたら適宜参照していただければ幸いです。

## 失敗する理由

ドキュメントでは実行イメージの拡張用 Dockerfile の内容が下記の通りとなっています。

```dockerfile:run.Dockerfile
FROM gcr.io/buildpacks/gcp/run
USER root
RUN apt-get update && apt-get install -y --no-install-recommends \
  imagemagick && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*
USER
```

失敗する理由は下記の 2 点です。

- 起点（FROM）イメージが古い。
- 最後の `USER` 行が不要。

起点イメージについては現時点では `FROM gcr.io/buildpacks/google-22/run` と指定する必要があるみたいです。これら 2 点を修正した Dockerfile を下記に示します。

```dockerfile:run.Dockerfile
FROM gcr.io/buildpacks/google-22/run
USER root
RUN apt-get update && apt-get install -y --no-install-recommends \
  imagemagick && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*
```

## 検証手順

以下、次のようなシナリオを想定して検証手順を説明します。

1. ImageMagick のバージョンを表示する Web サーバーを Node.js で作成
2. Docker で ImageMagick をインストールした実行イメージをビルド
3. Buildpacks で Web サーバーをビルド
4. Cloud Run へデプロイ

## Web サーバーの作成

### 準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```sh:コマンド
# Buildpacks をインストールします。
brew install buildpacks/tap/pack
# ImageMagick をインストールします。
brew install imagemagick
# ディレクトリを作成して移動します。
mkdir imagemagick-version && cd imagemagick-version
# ソースコードを作成します。
touch main.js package.json run.Dockerfile
```

### コーディング

エディタで下記のファイルを編集します。

```js:main.js
const http = require("http");
const { exec } = require("child_process");

/** Web サーバーのインスタンスです。 */
const server = http.createServer((req, res) => {
  // ImageMagick のバージョン取得コマンドを実行します。
  exec("convert -version", (err, stdout) => {
    // コンテンツタイプを text/plain に設定します。
    res.writeHead(200, {
      "content-type": "text/plain",
    });

    if (err) {
      // エラーの場合はエラーメッセージを送信します。
      res.write(`Error: ${err.message}`);
    } else {
      // 標準出力に書き込まれた ImageMagick のバージョン情報を送信します。
      res.write(stdout);
    }

    // HTTP レスポンスを完了します。
    res.end();
  });
});

/** Web サーバーを起動するポート番号です。 */
const port = parseInt(process.env.PORT, 10) || 3000;

server.listen(port, () => {
  // 起動したポート番号をコンソールに出力します。
  console.info(`Listening on ${port}`);
});
```

```json:package.json
{
  "scripts": {
    "start": "node main.js"
  }
}
```

### 動作確認

動作確認にはターミナルで下記のコマンドを実行します。curl の代わりにブラウザで確認しても OK です。

```sh:コマンド
# Web サーバーを起動します。
npm start
# "Listening on 3000" と表示されたら別ターミナルで下記を実行します。
curl http://localhost:3000
```

実行例を下記に示します。

```txt:コンソール出力（例）
Version: ImageMagick 7.1.1-20 Q16-HDRI x86_64 21621 https://imagemagick.org
Copyright: (C) 1999 ImageMagick Studio LLC
License: https://imagemagick.org/script/license.php
Features: Cipher DPC HDRI Modules OpenMP(5.0)
Delegates (built-in): bzlib fontconfig freetype gslib heic jng jp2 jpeg jxl lcms lqr ltdl lzma openexr png ps raw tiff webp xml zlib
Compiler: gcc (4.2)
```

## 実行イメージのビルド

### コーディング

エディタで下記のファイルを編集します。

```dockerfile:run.Dockerfile
FROM gcr.io/buildpacks/google-22/run
USER root
RUN apt-get update && apt-get install -y --no-install-recommends \
  imagemagick && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/*
```

### ビルド

ターミナルで下記のコマンドを実行して実行イメージをビルドします。

```sh:コマンド
docker build -t imagemagick-version-run -f run.Dockerfile .
```

### 動作確認

動作確認にはターミナルで下記のコマンドを実行します。

```sh:コマンド
docker run --rm imagemagick-version-run convert -version
```

実行例を下記に示します。

```txt:コンソール出力（例）
Version: ImageMagick 6.9.7-4 Q16 x86_64 20170114 http://www.imagemagick.org
Copyright: © 1999-2017 ImageMagick Studio LLC
License: http://www.imagemagick.org/script/license.php
Features: Cipher DPC Modules OpenMP
Delegates (built-in): bzlib djvu fftw fontconfig freetype jbig jng jpeg lcms lqr ltdl lzma openexr pangocairo png tiff wmf x xml zlib
```

## Web サーバーのビルド

### ビルド

ターミナルで下記のコマンドを実行して実行イメージをビルドします。

```sh:コマンド
pack build imagemagick-version \
  --builder gcr.io/buildpacks/builder \
  --run-image imagemagick-version-run
```

### 動作確認

ターミナルで下記のコマンドを実行します。curl の代わりにブラウザで確認しても OK です。

```sh:コマンド
# Web サーバーコンテナを起動します。
docker run --rm -e PORT=3000 -p 3000:3000 --name imagemagick-version imagemagick-version
# "Listening on 3000" と表示されたら別ターミナルで下記を実行します。
curl http://localhost:3000
# Web サーバーコンテナを停止します。
docker kill imagemagick-version
```

実行例を下記に示します。

```txt:コンソール出力（例）
Version: ImageMagick 6.9.7-4 Q16 x86_64 20170114 http://www.imagemagick.org
Copyright: © 1999-2017 ImageMagick Studio LLC
License: http://www.imagemagick.org/script/license.php
Features: Cipher DPC Modules OpenMP
Delegates (built-in): bzlib djvu fftw fontconfig freetype jbig jng jpeg lcms lqr ltdl lzma openexr pangocairo png tiff wmf x xml zlib
```

## Cloud Run へのデプロイ

### タグ付け

ターミナルで下記のコマンドを実行してプッシュできるようにタグ付けします。

```sh:コマンド
# gcloud コマンドの構成を確認します。
gcloud config configurations activate xxxx
# GCP のプロジェクト ID を取得します。
PROJECT_ID=`gcloud config get-value project`
# サービス名です。
SERVICE_NAME=imagemagick-version
# 参照されるイメージのタグです。
SOURCE_IMAGE=${SERVICE_NAME}
# 新たに作成されるタグです。
TARGET_IMAGE=asia-northeast1-docker.pkg.dev/${PROJECT_ID}/cloud-run-source-deploy/${SERVICE_NAME}
# タグ付けを実行します。
docker tag ${SOURCE_IMAGE} ${TARGET_IMAGE}
```

### プッシュ

ターミナルで下記のコマンドを実行して Artifact Registry にプッシュします。

```sh:コマンド
docker push ${TARGET_IMAGE}
```

### デプロイ

ターミナルで下記のコマンドを実行して Cloud Run にデプロイします。

```sh:コマンド
gcloud run deploy ${SERVICE_NAME} \
  --image ${TARGET_IMAGE} \
  --platform managed \
  --region asia-northeast1 \
  --allow-unauthenticated
```

### 動作確認

Cloud Run へのデプロイ後に発行される https://imagemagick-version-xxxx-an.a.run.app のようなサービス URL に curl やブラウザでアクセスします。実行例を下記に示します。

```txt:コンソール出力(例）
Version: ImageMagick 6.9.11-60 Q16 x86_64 2021-01-25 https://imagemagick.org
Copyright: (C) 1999-2021 ImageMagick Studio LLC
License: https://imagemagick.org/script/license.php
Features: Cipher DPC Modules OpenMP(4.5)
Delegates (built-in): bzlib djvu fftw fontconfig freetype heic jbig jng jp2 jpeg lcms lqr ltdl lzma openexr pangocairo png tiff webp wmf x xml zlib
```

## おまけ：起点イメージを探した方法

run.Dockerfile で `FROM gcr.io/buildpacks/gcp/run` と指定して実行イメージをビルドすると、pack コマンド実行時に下記のエラーが出力されます。

> ERROR: failed to build: invalid run-image 'imagemagick-version-run': run-image stack id 'google' does not match builder stack 'google.22'

上記を手がかりに Google Cloud Platform の buildpacks リポジトリを調べていたら下記の TOML ファイルを見つけました。

[https://github.com/GoogleCloudPlatform/buildpacks/blob/main/builders/gcp/base/google.22.builder.toml](https://github.com/GoogleCloudPlatform/buildpacks/blob/main/builders/gcp/base/google.22.builder.toml)

この TOML ファイルの最後の方の内容は下記の通りとなっています。

```toml:google.22.builder.toml（末尾のみ）
# Currently built with //builders/gcp/base/stack/stack:build.
[stack]
  id = "google.22"
  build-image = "gcr.io/buildpacks/google-22/build"
  run-image = "gcr.io/buildpacks/google-22/run"

[lifecycle]
  version = "0.17.2"
```

この点から「起点イメージとして `FROM gcr.io/buildpacks/google-22/run` と指定する必要があるのかな？」と思い、試してみたところ成功しました。

## おわりに

普通にソースコードからデプロイしたらどうなるか気になったので試してみました。

```sh:コマンド
gcloud run deploy ${SERVICE_NAME} \
  --source . \
  --platform managed \
  --region asia-northeast1 \
  --allow-unauthenticated
```

結果としては（期待通り）エラーメッセージが表示されました。

```txt:コンソール出力（例）
Error: Command failed: convert -version
/bin/sh: 1: convert: not found
```

Cloud Run 側で run.Dockerfile があった時に自動で実行イメージを作成してくれたら最高にクールなのですが、ドキュメントなどを調べた限り現時点ではそのような機能は無さそうです。

Cloud Build でも実行イメージの指定はできないようですが、下記のようにビルダーイメージの指定はできるようです。

```sh:コマンド
gcloud builds submit --pack builder=BUILDER_IMAGE_URL,image=LOCATION-docker.pkg.dev/PROJECT_ID/REPO_NAME/SERVICE_IMAGE_NAME
```

https://cloud.google.com/docs/buildpacks/use-a-specific-builder?hl=ja

僕が知らないだけで Cloud Run や Cloud Build で実行イメージの自動作成や指定は可能なのかもしれません。もしご存知の方がいましたらコメントで教えていただけると嬉しいです。
