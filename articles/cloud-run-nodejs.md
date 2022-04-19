---
title: "Node.jsでCloud Runにコンテナをデプロイする方法"
emoji: "📦"
type: "tech"
topics: ["nodejs"]
published: true
---

# この記事について

この記事では [Buildpacks](https://buildpacks.io/) を使って作成したコンテナイメージを [Cloud Run](https://cloud.google.com/run) にデプロイする方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. コンテナイメージの作成
3. デプロイ
4. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-cloudrun-nodejs
cd column-cloudrun-nodejs
touch main.js
touch package.json
```

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/d73440930fbc2202664eb12456136934?file=main.js)

ポイントを下記に示します。

1. Webサーバーを生成しています。
2. ポートの待ち受けを開始した時のイベントハンドラを登録しています。
3. リクエストを受信した時のイベントハンドラを登録しています。
4. エラーが発生した時のイベントハンドラを登録しています。
5. 環境変数PORTで指定されたポートでリクエストの待ち受けを開始します。Cloud Runはコンテナが実行されるときにCloud Runによって自動的に指定されます。その他の要件については [Container runtime contract](https://cloud.google.com/run/docs/container-contract#env-vars) のページに詳述されています。

続いてエディタでpackage.jsonを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/d73440930fbc2202664eb12456136934?file=package.json)

ポイントを下記に示します。

1. startはコンテナ起動時に実行されるコマンドです。このコマンドだけはBuildpacksを使ってビルドするために必須であり、それ以外のコマンドはビルドの手順を簡単にするために登録しています。
2. buildはコンテナイメージをビルドするコマンドです。[Pack](https://buildpacks.io/docs/tools/pack/) コマンドがインストールされている必要があります。
3. imageはコンテナイメージの名称を出力するコマンドです。他のコマンドから呼び出されます。
3. tagはコンテナイメージをプッシュするためにタグを付けるコマンドです。
4. pushはコンテナイメージをプッシュするコマンドです。
5. deployはコンテナをCloud Runにデプロイするコマンドです。
6. stagingはビルドからデプロイまでのコマンドをまとめたコマンドです。
7. engines > nodeに明記することでBuildpacksでビルドする時のNode.jsのバージョンを指定することができます。



# コンテナイメージの作成

下記のコマンドを実行してコンテナイメージを作成します。

```shell
npm run build
```

コマンドを実行するとビルドに必要なコンテナイメージの取得が開始します。

![コマンドの実行が開始した直後の様子です。latest: Pulling from buildpacks/builderとメッセージが表示された後、53e5e158da5aなどのコンテナイメージのハッシュ値と取得状態（Already existsなど）が表示されます。](https://storage.googleapis.com/zenn-user-upload/640f0a67b48e-20220419.png)

ビルドが終了すると「Successfully built image column-cloudrun-nodejs」とメッセージが表示されます。

![コマンドが実行が終了した直後の状態です。Successfully built image column-cloudrun-nodejsとメッセージが表示されています。](https://storage.googleapis.com/zenn-user-upload/3ee7c82ca3aa-20220419.png)



# デプロイ

下記のコマンドを実行してコンテナイメージをCloud Runにデプロイします。

```shell
npm run deploy
```

コマンドを実行するとService URLが表示されるので後の手順のために控えます。

![コマンドの実行結果です。メッセージの最後にService URLが表示されます。](https://storage.googleapis.com/zenn-user-upload/a0cb6fc86950-20220419.png)



# 動作確認

Webブラウザで先の手順で控えたService URLにアクセスして `{"ok":true}` と表示されることを確認します。

![Webブラウザでアクセスした時の様子です。{"ok":true}と表示されています。](https://storage.googleapis.com/zenn-user-upload/7ae331abffad-20220419.png)

動作確認が完了したら下記のコマンドを実行して後片付けをします。

```shell
npm run delete
```

コマンドを実行すると「o you want to continue (Y/n)? 」と削除しても良いか尋ねられるのでエンターキーを入力します。

![コマンドを実行した直後の様子です。Do you want to continue (Y/n)? と確認メッセージが表示されます。](https://storage.googleapis.com/zenn-user-upload/497103e4c21b-20220419.png)



# おわりに

この記事ではコンテナイメージのビルド／プッシュを手動で行いましたが、下記のコマンドを実行することでソースコードから自動でビルド／プッシュすることも可能です。

```
gcloud run deploy
```

また、この記事では割愛してしまいましたが [Artifact Registry](https://cloud.google.com/artifact-registry) にDockerレポジトリを作成するのに加え、Dockerコマンドを実行して認証を行う必要があります。
認証の手順については [公式ドキュメント](https://cloud.google.com/artifact-registry/docs/docker/store-docker-container-images#auth) に詳しく記載されています。

Cloud Runは素晴らしいサービスだと思うのでこの記事がはじめて利用する方などの一助となれば幸いです。
最後まで読んでいただきありがとうございました！
