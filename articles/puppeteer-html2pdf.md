---
title: "PuppeteerでHTML→PDF変換 on Cloud Run"
emoji: "❎"
type: "tech"
topics: ["nodejs", "puppeteer", "cloudrun", "pdf"]
published: true
---



## この記事について

この記事では[Puppeteer](https://pptr.dev/)を使ってHTMLをPDFに変換するAPIを[Cloud Run](https://cloud.google.com/run)上に構築する方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/fc83f928ec4c849b228b4474a9c0b9d4)



## おおまかな流れ

おおまかな流れを下記に示します。

1. コーディングの準備
2. コーディング
3. ローカルでの動作確認
4. Dockerfileの作成
5. コンテナイメージのビルド
6. コンテナでの動作確認
7. Cloud Runへのデプロイ
8. Cloud Runでの動作確認
9. 後片付け


## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
npm init -y
npm install --save dotenv express puppeteer
touch .dockerignore .env api-render.mjs Dockerfile main.mjs test.html
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### main.mjs

@[gist](https://gist.github.com/tatsuyasusukida/fc83f928ec4c849b228b4474a9c0b9d4?file=main.mjs)

### api-render.mjs

@[gist](https://gist.github.com/tatsuyasusukida/fc83f928ec4c849b228b4474a9c0b9d4?file=api-render.mjs)

ポイントを下記に示します。

1. Puppeteerの起動オプションに--no-sandboxを指定します。--no-sandboxは指定しない方が望ましいですが、コンテナを起動する時に--cap-add=SYS_ADMINが必要になるのでCloud Runで実行できなくなくなります。
2. page.setContentのwaitUntilオプションに'networkidle0'を指定します。これにより、Webフォントなどの読み込みを待機することができます。

### .env

@[gist](https://gist.github.com/tatsuyasusukida/fc83f928ec4c849b228b4474a9c0b9d4?file=.env)



## ローカルでの動作確認

下記のコマンドを実行してサーバーを起動します。

```
node -r dotenv/config main.mjs
```

エディタでtest.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/fc83f928ec4c849b228b4474a9c0b9d4?file=test.html)

下記のコマンドを実行してtest.htmlをtest.pdfに変換します。

```
cat test.html | curl http://localhost:3000/api/render -X POST --data-binary @- -v > test.pdf
```

test.pdfを開いて内容を確認します。

![タイトルは「Puppeteer HTML to PDF converter」です。本文は「日本語も利用可能です。」です](/images/articles/puppeteer-html2pdf/img-check-local-01.jpg)



## Dockerfileの作成

エディタで下記のファイルを開いて内容を入力します。

### Dockerfile

@[gist](https://gist.github.com/tatsuyasusukida/fc83f928ec4c849b228b4474a9c0b9d4?file=Dockerfile)

Dockerfileの内容については下記2点の合わせ技です。

- [puppeteer/troubleshooting.md](https://github.com/puppeteer/puppeteer/blob/main/docs/troubleshooting.md#running-puppeteer-in-docker)
- [Dockerizing a Node.js web app](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)

なお、Webフォントを利用する場合は下記のパッケージをインストールしないことで約50MBほどコンテナイメージのサイズを削減することができます。

- fonts-ipafont-gothic
- fonts-wqy-zenhei
- fonts-thai-tlwg
- fonts-kacst
- fonts-freefont-ttf

### .dockerignore

@[gist](https://gist.github.com/tatsuyasusukida/fc83f928ec4c849b228b4474a9c0b9d4?file=Dockerfile)



## コンテナイメージのビルド

下記のコマンドを実行してコンテナイメージをビルドします。

```shell
docker image build -t puppeteer-html2pdf .
```

出力結果を下記に示します。

```
[+] Building 6.8s (11/11) FINISHED                                              
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 743B                                       0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 34B                                           0.0s
 => [internal] load metadata for docker.io/library/node:lts-slim           2.4s
 => [1/6] FROM docker.io/library/node:lts-slim@sha256:297b6dbff6c1643db17  0.0s
 => [internal] load build context                                          0.0s
 => => transferring context: 13.88kB                                       0.0s
 => CACHED [2/6] RUN apt-get update     && apt-get install -y wget gnupg   0.0s
 => CACHED [3/6] WORKDIR /usr/src/app                                      0.0s
 => [4/6] COPY package*.json ./                                            0.0s
 => [5/6] RUN npm ci --only=production                                     3.9s
 => [6/6] COPY . .                                                         0.0s 
 => exporting to image                                                     0.3s 
 => => exporting layers                                                    0.3s 
 => => writing image sha256:45ea456be2d971c2ae97cbf155aac0fb7f0cee5118c21  0.0s 
 => => naming to docker.io/library/puppeteer-html2pdf                      0.0s

```


## コンテナでの動作確認

下記のコマンドを実行してコンテナを起動します。

```shell
docker container run --init -it --rm \
  --name puppeteer-html2pdf \
  --env PORT=3000 \
  --env PUPPETEER_EXECUTABLE_PATH=/usr/bin/google-chrome-stable \
  -p 3000:3000 \
  puppeteer-html2pdf
```

下記のコマンドを実行してtest.htmlをtest.pdfに変換します。なお、コマンドの内容はローカルでの動作確認時と同じです。

```
cat test.html | curl http://localhost:3000/api/render -X POST --data-binary @- -v > test.pdf
```



## Cloud Runへのデプロイ

下記のコマンドを実行してCloud Runへデプロイします。

```
IMAGE=asia-northeast1-docker.pkg.dev/`gcloud config get-value project`/cloud-run/puppeteer-html2pdf

# Artifact Registryにプッシュするためにタグ付けします
docker tag puppeteer-html2pdf ${IMAGE}

# Artifact Registryにコンテナイメージをプッシュします
docker push ${IMAGE}

# Cloud Runにコンテナイメージをデプロイします
gcloud run deploy puppeteer-html2pdf \
  --image ${IMAGE} \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated \
  --set-env-vars "PUPPETEER_EXECUTABLE_PATH=/usr/bin/google-chrome-stable"
```



## Cloud Runでの動作確認

下記のコマンドを実行してtest.htmlをtest.pdfに変換します。なお、コマンドの内容はURLを除いてローカルおよびコンテナでの動作確認時と同じです。

```shell
cat test.html | curl https://puppeteer-html2pdf-xxxxxxxxxx-an.a.run.app/api/render -X POST --data-binary @- -v > test.pdf
```



## 後片付け

下記のコマンドを実行してCloud Runへデプロイしたサービスを削除します。

```shell
gcloud run services delete puppeteer-html2pdf \
  --region asia-northeast1 \
  --platform managed
```



## おわりに

現在のままでは誰でもAPIにアクセスできる状態なので、アクセスを制限するにはBasic認証やOAuthを設定することが望ましいです。設定方法については関連記事で紹介しているので必要に応じてご参照ください。



## 関連記事

- [Node.jsでBasic認証を設定する方法](https://zenn.dev/tatsuyasusukida/articles/981a61bca873da)
- [Node.jsでAuth0を利用してAPI認可を実装する](https://zenn.dev/tatsuyasusukida/articles/auth0-webapi)
