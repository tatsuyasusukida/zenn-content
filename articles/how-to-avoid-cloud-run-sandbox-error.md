---
title: "Cloud RunのContainer terminated due to sandbox errorを回避する方法"
emoji: "🏜"
type: "tech"
topics: ["gcp", "cloudrun", "cloud", "googlecloud"]
published: true
---

## この記事について

この記事では[Cloud Run](https://cloud.google.com/run)でソースコードからデプロイをしている時にContainer terminated due to sandbox errorが発生し、約1時間の格闘の末にエラーを回避する方法を見つけたので再現手順や回避方法について紹介します。この記事の関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/how-to-avoid-cloud-run-sandbox-error)



## エラー発生条件

Cloud Runでソースコードからデプロイする時にNode.jsの場合はpackage.jsonのstartスクリプトがコンテナの起動コマンドとなります。例えばpackage.jsonの内容が下記の場合は`node server.js`がコンテナの起動コマンドとなります。

```json:package.json
{
  "scripts": {
    "start": "node server.js"
  }
}
```

一方、Cloud Runでは下記のようにデプロイ時に起動コマンドを指定することができます。これにより、同じコンテナイメージを使って異なるサービスを起動することができます（例：Webサーバー＋バッチ処理用のワーカー）。

```shell
gcloud run deploy worker \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated \
  --command node \
  --args worker.js
```

しかしながら、Cloud Runでソースコードからデプロイしている＆デプロイ時に起動コマンドを指定しているとコンテナの起動に失敗して下記のエラーが発生します。

```
Container terminated due to sandbox error.
```



## 再現手順

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir how-to-avoid-cloud-run-sandbox-error
cd how-to-avoid-cloud-run-sandbox-error
touch main.js sub.js package.json
```

エディタで下記のファイルを開いて内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/dc5a008f84860ca43bdc82e94eed1366?file=main.js)

@[gist](https://gist.github.com/tatsuyasusukida/dc5a008f84860ca43bdc82e94eed1366?file=sub.js)

```json:package.json
{
  "scripts": {
    "start": "node main.js"
  }
}
```

下記のコマンドを実行してCloud Runにデプロイします。

```shell
gcloud run deploy service-name \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated \
  --command node \
  --args sub.js
```

デプロイが失敗することを確認します。また、Cloud Runにログに下記のメッセージが記録されていることを確認します。

```
Container terminated due to sandbox error.
```



## 回避方法1：ディレクトリを分ける

下記のようにスクリプトごとにディレクトリを分けます。

```shell
mkdir main
touch main/main.js
touch main/package.json

mkdir sub
touch sub/sub.js
touch sub/package.json
```

それぞれのpackage.jsonのstartスクリプトに起動コマンドを別々に指定します。

```json:main/package.json
{
  "scripts": {
    "start": "node main.js"
  }
}
```

```json:sub/package.json
{
  "scripts": {
    "start": "node sub.js"
  }
}
```

コマンドライン引数の--commandと--argsを指定しないでデプロイします。

```shell
cd main
gcloud run deploy main \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated \
```

```shell
cd sub
gcloud run deploy sub \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated \
```

データベースのモデルなど共通で使用したいファイルがある場合はgitのsubmodule機能などを利用します。



## 回避方法2：起動コマンドを同じにする

下記のようにスクリプトに加えて起動するスクリプトを選択するスクリプト（select.js）を作成します。

```shell
touch script-main.js
touch script-sub.js
touch select.js
touch package.json
```

main.jsとsub.jsのスクリプトをインポートできるように書き換えます。

@[gist](https://gist.github.com/tatsuyasusukida/dc5a008f84860ca43bdc82e94eed1366?file=script-main.js)

@[gist](https://gist.github.com/tatsuyasusukida/dc5a008f84860ca43bdc82e94eed1366?file=script-sub.js)

select.jsでは環境変数によって起動するスクリプトを決定します。

@[gist](https://gist.github.com/tatsuyasusukida/dc5a008f84860ca43bdc82e94eed1366?file=select.js)

package.jsonのstartスクリプトには`node select.js`を指定します。

```json:sub/package.json
{
  "scripts": {
    "start": "node select.js"
  }
}
```

デプロイ時に環境変数を使って起動したいスクリプトを指定します。

```shell
gcloud run deploy sub \
  --source . \
  --region asia-northeast1 \
  --platform managed \
  --allow-unauthenticated \
  --set-env-vars SCRIPT=sub.js
```



## おわりに

できればエラーの原因を調査して解決したかったですが[Buildpacks](https://buildpacks.io/docs/)のドキュメントを読む時間と元気がなかったので途中で諦めて回避する方針に切り替えました。`gcloud run deploy`のコマンドライン引数の--commandや--argsが間違っているだけで適切な内容を指定すればエラーは発生しないのかも知れません。何かがわかった時にはまた報告したいと思います。
