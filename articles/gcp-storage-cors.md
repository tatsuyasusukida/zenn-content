---
title: "Google Cloud StorageでCORSを設定する方法【動画版あり】"
emoji: "🚫"
type: "tech"
topics: ["gcp", "cors"]
published: true
---

## この記事について

この記事ではGoogle Cloud Storage（以下「GCS」と呼びます）で [CORS](https://developer.mozilla.org/ja/docs/Web/HTTP/CORS) を設定し、Webページからfetchなどを使ってアクセスできるようにする方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/72802c4a286bfc2417992a4efb169979#file-config-example-json)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/72802c4a286bfc2417992a4efb169979)
- [動画版](https://www.youtube.com/watch?v=MdbcYhWF8eI)

https://www.youtube.com/watch?v=MdbcYhWF8eI



## おおまかな手順

おおまかな手順を下記に示します。

1. バケットの作成
2. 権限の設定
3. 動作確認用オブジェクトのアップロード
4. CORSの設定
5. コーディングの準備
6. コーディング
7. 動作確認



## バケットの作成

[GCSのWebコンソール](https://console.cloud.google.com/storage/) にアクセスして下記のバケットを作成します。

- バケット名：任意（例：gcp-storage-cors-(日付8桁)）
- ロケーションタイプ：リージョン
- リージョン：asia-northeast1 (東京)
- ストレージクラス：Standard
- 公開アクセスの防止：チェックしない
- アクセス制御：均一
- 保護ツール：なし

### 参考画像

![](/images/articles/gcp-storage-cors/_img-bucket-01.jpg)

![](/images/articles/gcp-storage-cors/_img-bucket-02.jpg)

![](/images/articles/gcp-storage-cors/_img-bucket-03.jpg)



## 権限の設定

作成したバケットの詳細ページ（オブジェクトの一覧ページ）にアクセスして下記の権限を追加します。

- プリンシパル：allUsers
- ロール：Storage レガシー オブジェクト 読み取り

### 参考画像

![](/images/articles/gcp-storage-cors/_img-auth-01.jpg)

![](/images/articles/gcp-storage-cors/_img-auth-02.jpg)

![](/images/articles/gcp-storage-cors/_img-auth-03.jpg)

![](/images/articles/gcp-storage-cors/_img-auth-04.jpg)

![](/images/articles/gcp-storage-cors/_img-auth-05.jpg)



## 動作確認用オブジェクトのアップロード

作成したバケットの詳細ページで「ファイルをアップロードする」ボタンをクリックするなどして下記の動作確認用オブジェクトをアップロードします。

- 名前：status.json
- 内容：{"ok":true}

### 参考画像

![](/images/articles/gcp-storage-cors/_img-upload-01.jpg)

![](/images/articles/gcp-storage-cors/_img-upload-02.jpg)

![](/images/articles/gcp-storage-cors/_img-upload-03.jpg)



## CORSの設定

ターミナルで下記のコマンドを実行して設定の準備をします。

```shell
touch cors.json
```

エディタでcors.jsonを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/72802c4a286bfc2417992a4efb169979?file=cors.json)

ターミナルで下記のコマンドを実行してCORSの設定します。

```shell
gsutil cors set cors.json gs://gcp-storage-cors-00000000
```


## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir gcp-storage-cors
cd gcp-storage-cors
npm init -y
npm install --save-dev http-server
touch config.json index.html main.js
```



## コーディング

### config.json

エディタでconfig.jsonを開いて動作確認用オブジェクトの公開URLを入力します。公開URLはGCS Webコンソールのオブジェクト詳細ページからも確認することができます。

@[gist](https://gist.github.com/tatsuyasusukida/72802c4a286bfc2417992a4efb169979?file=config.example.json)

### index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/72802c4a286bfc2417992a4efb169979?file=index.html)

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/72802c4a286bfc2417992a4efb169979?file=main.js)



## 動作確認

ターミナルで下記のコマンドを実行してWebサーバーを起動します。

```
npx http-server -c-1
```

ブラウザで http://localhost:8080/ にアクセスしてWebページ内に下記のテキストが表示されることを確認します。

```
{"ok":true}
```



## おわりに

GCSのCORS設定が反映されるまで数分程度時間がかかる様子なので、うまくいかない場合は少し時間を置いてから再度お試しください。最後までお読みいただきありがとうございました。
