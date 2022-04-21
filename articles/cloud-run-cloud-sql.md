---
title: "Node.js＋SequelizeでCloud RunからCloud SQLへ接続する方法"
emoji: "🏃"
type: "tech"
topics: ["nodejs", "cloudrun"]
published: true
---

# はじめに

この記事ではCloud Runで実行されるNode.js＋SequelizeのコードからCloud SQLへ接続する方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. デプロイ
3. データベースの準備
4. Cloud SQL Admin APIの有効化
5. IAMの設定
6. Cloud Runの設定
7. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir cloudrun-cloudsql
cd cloudrun-cloudsql
npm init -y
npm install --save express mysql2 sequelize
touch main.js
```

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/c5227e83771766a04b88ca6af7b81369?file=main.js)

続いてエディタでpackage.jsonを開いてscriptsに `"start": "node main.js",`を追記します。

@[gist](https://gist.github.com/tatsuyasusukida/c5227e83771766a04b88ca6af7b81369?file=package-example.json)



# デプロイ

下記のコマンドを実行してCloud Runへデプロイします。

```shell
gcloud run deploy cloudrun-cloudsql \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated
```

デプロイが完了するとService URLが表示されるので後の手順のために控えます。

![上記のコマンドの実行結果です、Service URLが含まれています。](/images/articles/cloud-run-cloud-sql/deploy-01.png)

ちなみにこの状態でブラウザでService URLにアクセスすると必要な設定が済んでいないためエラーメッセージが表示されます。

![ブラウザでService URLにアクセスした時の様子です、Dialect needs to be explicitly supplied as of v4.0.0 とエラーメッセージが表示されています。](/images/articles/cloud-run-cloud-sql/deploy-02.png)



# データベースの準備

ブラウザでCloud SQLのインスタンスのページにアクセスして下記2点を後の手順のために控えます。この際、コピーボタンを利用すると便利です。

- パブリックIPアドレス（例：12.34.56.78）
- 接続名（例：project-id:asia-northeast1:db-instance）

![Cloud SQLのインスタンスページです、このインスタンスとの接続セクションにパブリックIPアドレスと接続名の表示部が含まれています。](/images/articles/cloud-run-cloud-sql/db-01.png)

続いてCloud Shellを利用するなどして下記のSQL文を実行します。

@[gist](https://gist.github.com/tatsuyasusukida/c5227e83771766a04b88ca6af7b81369?file=db.sql)



# Cloud SQL Admin APIの有効化

GCPのWebコンソールのキーワード検索で「Cloud SQL Admin API」と入力するなどして [Cloud SQL Admin APIのページ](https://console.cloud.google.com/marketplace/product/google/sqladmin.googleapis.com) に移動してAPIを有効化します。

![GCPのWebコンソールの検索キーワードに「Cloud SQL Admin API」と入力している様子です](/images/articles/cloud-run-cloud-sql/api-01.png)



# IAMの設定

ブラウザで [IAMのページ](https://console.cloud.google.com/iam-admin/iam) にアクセスします。

![IAMのページです、アカウントの一覧が表示されています。](/images/articles/cloud-run-cloud-sql/iam-01.png)

アカウントの一覧の中からCloud Runのコンテナを実行するサービスアカウント（デフォルトではCompute Engine default service account）を探して右側にあるペンのアイコンのボタンをクリックすると権限を編集するモーダルが表示されるので「Cloud SQL クライアント」のロールを追加します。

![権限を編集するモーダルでCloud SQL クライアントのロールを追加している様子です。](/images/articles/cloud-run-cloud-sql/iam-02.png)

保存ボタンをクリックして変更を保存します。

![権限を編集するモーダルの最後の方に保存ボタンがあります。](/images/articles/cloud-run-cloud-sql/iam-03.png)




# Cloud Runの設定

ブラウザでCloud Runのサービスの詳細ページにアクセスしてページの上側にある「新しいリビジョンの編集とデプロイ」リンクをクリックします。

![Cloud Runのサービスの詳細ページです、新しいリビジョンの編集とデプロイリンクが含まれています。](/images/articles/cloud-run-cloud-sql/run-01.png)

新しいリビジョンをデプロイするページが表示されたら「変数とシークレット」タブをクリックします。

![新しいリビジョンをデプロイするページです、コンテナタブや変数とシークレットタブなどが含まれています。](/images/articles/cloud-run-cloud-sql/run-02.png)

「変数を追加」ボタンをクリックすると環境変数の名前と値の入力部が追加されるので下記の内容を入力します。なお、値の「12.34.56.78」と「/cloudsql/project-id:asia-northeast1:db-instance」の部分は先の手順で控えたパブリックIPアドレスと接続名にそれぞれ置き換えます。

- 名前: DB_URL
- 値: mysql://dbuser:dbpass@12.34.56.78/dbname?socketPath=/cloudsql/project-id:asia-northeast1:db-instance

![変数とシークレットを入力するページです、変数を追加ボタンなどが含まれています。](/images/articles/cloud-run-cloud-sql/run-03.png)

続いて「接続」タブをクリックしてから「接続を追加」ボタンをクリックするとCloud SQL インスタンスを選択するセレクトボックスが表示されるのでクリックして選択します。

![接続ページです、Cloud SQL 接続セクションに接続を追加ボタンが含まれています。](/images/articles/cloud-run-cloud-sql/run-04.png)

ページの最後の方にある「デプロイ」ボタンをクリックしてデプロイを開始します。

![接続ページの最後の方にデプロイボタンが表示されています](/images/articles/cloud-run-cloud-sql/run-05.png)

デプロイが完了したらサービスの変更内容のページにリビジョンが追加されます。

![Cloud Runのサービスの変更内容のページです、新しいリビジョンが追加されています。](/images/articles/cloud-run-cloud-sql/run-06.png)



# 動作確認

ブラウザでService URLにアクセスして `[{"status":"OK"}]` と表示されることを確認します。

![ブラウザでService URLにアクセスした様子です、{"status":"OK"} と表示されています。](/images/articles/cloud-run-cloud-sql/check-01.png)



# 参考にしたWebページ

記事を作成する上で参考にしたWebページを下記に示します。

- [Cloud Run から接続する | Cloud SQL for MySQL | Google Cloud](https://cloud.google.com/sql/docs/mysql/connect-run)



# おわりに

Cloud RunからCloud SQLへ接続するには下記3点の設定を行う必要があるので面倒です。

- Cloud SQL Admin APIの有効化
- IAMの設定
- Cloud Runの設定

同じプロジェクトであればCloud SQL Admin APIの有効化とIAMの設定は1回設定すればOKなので楽になります。

この記事ではCloud Runの [ソースコードからデプロイする機能](https://cloud.google.com/run/docs/deploying-source-code) を利用しましたが、このやり方ではビルドに1〜2分くらい時間がかかるのでトライ＆エラーを繰り返す場合は待ち時間が長くなります。

別の記事で [Node.jsでCloud Runにコンテナをデプロイする方法](https://zenn.dev/tatsuyasusukida/articles/cloud-run-nodejs) について紹介しており、こちらの記事では自前でコンテナイメージを作成する方法について説明しているので興味がありましたら併せてご覧いただければ幸いです。

この記事でCloud RunからCloud SQLになかなか接続できなくて困っている方の参考になれば幸いです。
最後まで読んでいただきありがとうございました！
