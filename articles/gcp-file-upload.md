---
title: "Google Cloud Storageにファイルをアップロードする方法"
emoji: "🗃️"
type: "tech"
topics: ["gcp", "cloudstorage", "storage"]
published: true
---

## この記事について

この記事ではブラウザからGoogle Cloud Storage（以下「GCS」と呼びます）のバケットにファイルをアップロードする方法について紹介します。この記事の関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/1e782b0042f5fe5b12c9a5639b61635a)



## アプローチ

ブラウザからGCSのバケットにファイルをアップロードする方法として下記の2つのアプローチが考えられます。

- 直接法：バケットに直接アップロードする方法
- 間接法：サーバーを経由してアップロードする方法

前者の直接法のイメージを下記に示します。

```mermaid
graph LR
  C[ブラウザ] -->|ファイル| B[バケット]
```

後者の間接法のイメージを下記に示します。

```mermaid
graph LR
  C[ブラウザ] -->|ファイル| S[サーバー] -->|ファイル| B[バケット]
```

この記事では後者の間接法について紹介します。前者の直接法と比べた場合の間接法のメリットとデメリットを下記に示します。

### 間接法のメリット

- バケットのCORS設定が不要
- バケット数が1つで済む（アップロード用と保存用を別々に用意する必要がない）
- 署名付きURLの発行が不要（サービス アカウント トークン作成者などのIAM設定が不要）

### 間接法のデメリット

- Cloud Runを使用する場合はリクエストの最大サイズが32MiBに制限される

### 選定基準

Cloud Runを使用し、アップロードするファイルのサイズが大きい場合は前者の直接法を選定します。それ以外の場合は間接法を選定した方が実装が簡単です。以下、間接法の実装手順を示します。



## バケットの作成

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
gsutil mb -l asia-northeast1 gs://gcp-file-upload-00000000
```

00000000の部分には今日の日付8桁などを入力します。



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir gcp-file-upload
cd gcp-file-upload
npm init -y
npm install --save @google-cloud/storage dotenv express
touch .env client.js index.html server.mjs upload.mjs
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### server.mjs

@[gist](https://gist.github.com/tatsuyasusukida/1e782b0042f5fe5b12c9a5639b61635a?file=server.mjs)

### upload.mjs

@[gist](https://gist.github.com/tatsuyasusukida/1e782b0042f5fe5b12c9a5639b61635a?file=upload.mjs)

### index.html

@[gist](https://gist.github.com/tatsuyasusukida/1e782b0042f5fe5b12c9a5639b61635a?file=index.html)

### client.js

@[gist](https://gist.github.com/tatsuyasusukida/1e782b0042f5fe5b12c9a5639b61635a?file=client.js)

### .env

@[gist](https://gist.github.com/tatsuyasusukida/1e782b0042f5fe5b12c9a5639b61635a?file=.env.example)

BUCKETの00000000の部分には今日の日付8桁などを入力します。



## 動作確認

下記のコマンドを実行してアプリケーションのデフォルト認証情報を取得します。

```shell
gcloud auth application-default login
```

ブラウザで http://localhost:3000 にアクセスします。

適当なファイルを選択してからUploadボタンをクリックします。

[動作確認用のテキストファイル](https://gist.githubusercontent.com/tatsuyasusukida/1e782b0042f5fe5b12c9a5639b61635a/raw/fb78371ed79ea033ef1ff2cc0125410ab263fc9b/test.txt)

リストにURLが表示されたらクリックして内容が表示されることを確認します。

### 参考画像

![](/images/articles/gcp-file-upload/img-check-01.png)

![](/images/articles/gcp-file-upload/img-check-02.png)

![](/images/articles/gcp-file-upload/img-check-03.png)



## おわりに

直接法：バケットに直接アップロードする方法の場合、いつもCORS設定やIAM設定に悩まされるのでファイルサイズが小さい場合は間接法：サーバーを経由してアップロードする方法を使う方が簡単です。

なお、Cloud Runなどで実行する場合はDefault compute service accountにStorage オブジェクト 作成者などのロールを忘れずに設定します。
