---
title: "Cloud Runのソースコードからデプロイが遅い時の代替手段"
emoji: "🚅"
type: "tech"
topics: ["cloudrun", "buildpacks"]
published: true
---

## この記事について

[Cloud Run](https://cloud.google.com/run)の[ソースコードからデプロイ](https://cloud.google.com/run/docs/deploying-source-code)は便利ですが完了までに数分程度を要するのでデプロイを何度も繰り返す場面では遅いと感じることがあります。この記事では少しでも時間を短縮するための代替手段として[Buildpacks](https://buildpacks.io/)を利用してローカルでコンテナイメージをビルドしてCloud Runにデプロイする方法について紹介します。なお、実行時間を比較したところ実行時間が約40%短縮されました。



## Packのインストール

コンテナイメージをビルドするために必要なPackをインストールします。macOSの場合は下記のコマンドを実行します。

```shell
brew install buildpacks/tap/pack
```

LinuxやWindowsの場合は[Pack・Cloud Native Buildpacks](https://buildpacks.io/docs/tools/pack/#install)のページから手順を確認することができます。



## ビルドからデプロイまでの手順

ビルドからデプロイまでの手順を下記に示します。

```shell
IMAGE=asia-northeast1-docker.pkg.dev/`gcloud config get-value project`/cloud-run/cloud-run-buildpacks

# docker buildの代わりにpackコマンドを実行してビルドします
pack build cloud-run-buildpacks \
  --builder gcr.io/buildpacks/builder

# Artifact Registryにプッシュするためにタグ付けします
docker tag cloud-run-buildpacks ${IMAGE}

# Artifact Registryにコンテナイメージをプッシュします
docker push ${IMAGE}

# Cloud Runにコンテナイメージをデプロイします
gcloud run deploy column-cloudrun-nodejs \
  --image ${IMAGE} \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated
```

package.jsonに下記のスクリプトを追加しておくと便利です。

```json
{
  "scripts": {
    "image": "echo asia-northeast1-docker.pkg.dev/`gcloud config get-value project`/cloud-run/cloud-run-buildpacks",
    "build": "pack build cloud-run-buildpacks --builder gcr.io/buildpacks/builder",
    "tag": "docker tag cloud-run-buildpacks `npm run -s image`",
    "push": "docker push `npm run -s image`",
    "deploy": "gcloud run deploy cloud-run-buildpacks --image `npm run -s image` --region asia-northeast1 --platform managed --allow-unauthenticated",
    "staging": "npm run build && npm run tag && npm run push && npm run deploy"
  }
}
```



## 所要時間の比較

下記のソースコードのデプロイに要する所要時間を比較します。

@[gist](https://gist.github.com/tatsuyasusukida/d73440930fbc2202664eb12456136934?file=main.js)

### 従来手法：ソースコードからデプロイ

コマンドを下記に示します。

```shell
gcloud run deploy cloud-run-buildpacks \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated
```

実行結果を下記に示します。

```
$ time gcloud run deploy cloud-run-buildpacks \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated
This command is equivalent to running `gcloud builds submit --pack image=[IMAGE] .` and `gcloud run deploy cloud-run-buildpacks --image [IMAGE]`

Building using Buildpacks and deploying container to Cloud Run service [cloud-run-buildpacks] in project [default-264513] region [asia-northeast1]
✓ Building and deploying... Done.                                              
  ✓ Uploading sources...                                                       
  ✓ Building Container... Logs are available at [https://console.cloud.google.c
  om/cloud-build/builds/802e3ed6-e54e-45c5-9944-71fb6af10672?project=4082803578
  06].                                                                         
  ✓ Creating Revision...                                                       
  ✓ Routing traffic...                                                         
  ✓ Setting IAM Policy...                                                      
Done.                                                                          
Service [cloud-run-buildpacks] revision [cloud-run-buildpacks-00002-doy] has been deployed and is serving 100 percent of traffic.
Service URL: https://cloud-run-buildpacks-xdblopffpq-an.a.run.app
gcloud run deploy cloud-run-buildpacks --source . --region asia-northeast1     1.44s user 0.31s system 2% cpu 1:24.05 total
```

実行結果から約1分24秒であることがわかります。

### 提案手法：ローカルでビルドしてデプロイ

コマンドを下記に示します。

```shell
npm run staging
````

実行結果を下記に示します。

```
$ time npm run staging

> staging
> npm run build && npm run tag && npm run push && npm run deploy


> build
> pack build cloud-run-buildpacks --builder gcr.io/buildpacks/builder

latest: Pulling from buildpacks/builder
Digest: sha256:8c1b577d79319fdf1e5948175a5dcd6afdc40108eee26aedd3434b9766c516a4
Status: Image is up to date for gcr.io/buildpacks/builder:latest
v1: Pulling from buildpacks/gcp/run
Digest: sha256:cadd899b2ffc67b43e5ed3b61386e23b1be90ca1b5ccc792ca4ed7b59f4ee226
Status: Image is up to date for gcr.io/buildpacks/gcp/run:v1
0.13.3: Pulling from buildpacksio/lifecycle
Digest: sha256:9d194fc3997e8c448473431433908d932cd359b10b270f2b0f76ec795c8cae28
Status: Image is up to date for buildpacksio/lifecycle:0.13.3
===> ANALYZING
===> DETECTING
[detector] 3 of 5 buildpacks participating
[detector] google.nodejs.runtime 1.0.0
[detector] google.nodejs.npm     1.0.0
[detector] google.utils.label    0.0.2
===> RESTORING
[restorer] Restoring metadata for "google.nodejs.runtime:node" from app image
[restorer] Restoring metadata for "google.nodejs.npm:npm_modules" from cache
[restorer] Restoring data for "google.nodejs.runtime:node" from cache
[restorer] Restoring data for "google.nodejs.npm:npm_modules" from cache
===> BUILDING
[builder] === Node.js - Runtime (google.nodejs.runtime@1.0.0) ===
[builder] 2022/06/30 00:24:15 [DEBUG] GET https://dl.google.com/runtimes/nodejs/version.json
[builder] Node.js v16.15.0 cache hit, skipping installation.
[builder] === Node.js - NPM (google.nodejs.npm@1.0.0) ===
[builder] Generating package-lock.json.
[builder] WARNING: *** Improve build performance by generating and committing package-lock.json.
[builder] --------------------------------------------------------------------------------
[builder] Running "npm install --package-lock-only --quiet"
[builder] 
[builder] up to date, audited 1 package in 210ms
[builder] 
[builder] found 0 vulnerabilities
[builder] Done "npm install --package-lock-only --quiet" (337.224688ms)
[builder] Dependencies cache hit, skipping installation.
[builder] --------------------------------------------------------------------------------
[builder] Running "npm install --quiet (NODE_ENV=production)"
[builder] 
[builder] up to date, audited 1 package in 193ms
[builder] 
[builder] found 0 vulnerabilities
[builder] Done "npm install --quiet (NODE_ENV=production)" (310.868888ms)
[builder] === Utils - Label Image (google.utils.label@0.0.2) ===
===> EXPORTING
[exporter] Reusing layer 'google.nodejs.runtime:node'
[exporter] Reusing layer 'google.nodejs.npm:env'
[exporter] Adding 1/1 app layer(s)
[exporter] Reusing layer 'launcher'
[exporter] Reusing layer 'config'
[exporter] Reusing layer 'process-types'
[exporter] Adding label 'io.buildpacks.lifecycle.metadata'
[exporter] Adding label 'io.buildpacks.build.metadata'
[exporter] Adding label 'io.buildpacks.project.metadata'
[exporter] Setting default process type 'web'
[exporter] Saving cloud-run-buildpacks...
[exporter] *** Images (9166969d6afa):
[exporter]       cloud-run-buildpacks
[exporter] Reusing cache layer 'google.nodejs.runtime:node'
[exporter] Reusing cache layer 'google.nodejs.npm:npm_modules'
Successfully built image cloud-run-buildpacks

> tag
> docker tag cloud-run-buildpacks `npm run -s image`


> push
> docker push `npm run -s image`

Using default tag: latest
The push refers to repository [asia-northeast1-docker.pkg.dev/default-264513/cloud-run/cloud-run-buildpacks]
83d85471d9f8: Layer already exists 
990283bedbbe: Layer already exists 
a4d53325badd: Layer already exists 
333f5ab5e0d0: Pushed 
8802793b6163: Layer already exists 
c3ed4693ea36: Layer already exists 
c1dc14219205: Layer already exists 
95d9b0288ba3: Layer already exists 
84ff92691f90: Layer already exists 
d7d23eae00b3: Layer already exists 
f48db787b119: Layer already exists 
latest: digest: sha256:b132082fb27369330e3545a3de984b1375102e730ef8d9c93d7ab0d67a55123e size: 2619

> deploy
> gcloud run deploy cloud-run-buildpacks --image `npm run -s image` --region asia-northeast1 --platform managed --allow-unauthenticated

Deploying container to Cloud Run service [cloud-run-buildpacks] in project [default-264513] region [asia-northeast1]
✓ Deploying... Done.                                                           
  ✓ Creating Revision...                                                       
  ✓ Routing traffic...                                                         
  ✓ Setting IAM Policy...                                                      
Done.                                                                          
Service [cloud-run-buildpacks] revision [cloud-run-buildpacks-00004-bir] has been deployed and is serving 100 percent of traffic.
Service URL: https://cloud-run-buildpacks-xdblopffpq-an.a.run.app
npm run staging  5.96s user 2.07s system 16% cpu 49.685 total
```

実行結果から約50秒であることがわかります。在来手法と比べて約34秒（約40%）短縮できていることがわかります。



## おわりに

ローカルでビルドしてデプロイする場合、初回はビルドに必要なコンテナイメージをダウンロードする必要があるのでソースからデプロイよりも実行時間が長くなります。また、2回目以降であっても通信速度が遅いやローカルマシンの性能が不足している場合はソースからデプロイよりも実行時間が長くなることがあります。
