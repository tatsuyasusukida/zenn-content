---
title: "Cloud Runでリダイレクトする方法"
emoji: "🏹"
type: "tech"
topics: ["go", "cloudrun"]
published: true
---

## この記事について

この記事では30行のGo言語プログラムを使って[Cloud Run](https://cloud.google.com/run)でリダイレクトする方法について紹介します。



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir cloud-run-redirect
cd cloud-run-redirect
go mod init example.com/redirect
touch main.go
```



## コーディング

エディタでmain.goを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/99b67b327f836fd531bee7e8211e702d?file=main.go)




## デプロイ

ターミナルで下記のコマンドを実行してCloud Runへデプロイします。

```shell
gcloud run deploy redirect \
    --source . \
    --region asia-northeast1 \
    --platform managed \
    --allow-unauthenticated \
    --set-env-vars "LOCATION=https://zenn.dev/"
```

実行結果の例を下記に示します。

```
Building using Buildpacks and deploying container to Cloud Run service [redirect] in project [xxxxxx-000000] region [asia-northeast1]
✓ Building and deploying new service... Done.                                  
  ✓ Uploading sources...
  ✓ Building Container... Logs are available at [https://console.cloud.google.com/cloud-build/builds/00000000-000000000-0000-000000000000?project=0000000000
  06].
  ✓ Creating Revision...
  ✓ Routing traffic...
  ✓ Setting IAM Policy...
Done.
Service [redirect] revision [redirect-00001-cus] has been deployed and is serving 100 percent of traffic.
Service URL: https://redirect-xxxxxxxxxx-an.a.run.app
```



## 動作確認

下記のコマンドを実行してCloud RunのサービスURLにアクセスします。

```shell
curl -v https://redirect-xxxxxxxxxx-an.a.run.app
```

実行結果を下記に示します。なお、HTTPレスポンスのみを抜粋しています。

```
< HTTP/2 301 
< location: https://zenn.dev/
< x-cloud-trace-context: 265574fea80ad062efcb0aa3285d0342;o=1
< date: Mon, 01 Aug 2022 06:41:56 GMT
< content-type: text/html
< server: Google Frontend
< content-length: 0
< alt-svc: h3=":443"; ma=2592000,h3-29=":443"; ma=2592000,h3-Q050=":443"; ma=2592000,h3-Q046=":443"; ma=2592000,h3-Q043=":443"; ma=2592000,quic=":443"; ma=2592000; v="46,43"
< 
```

実行結果から https://zenn.dev/ への301 Moved Permanentlyレスポンスが返送されていることが確認できます。



## おわりに

Cloud Runでリダイレクトするには下記の記事で紹介されているようにnginxなどWebサーバーのコンテナイメージを使う方法もあります。

[Cloud Runを使って無料でリダイレクトサーバをたてる｜Qiita](https://qiita.com/shoji-kai/items/82f89d7e62fd0bdaacae)

この記事で紹介した方法のメリットとしてはデプロイ後に環境変数を通じてリダイレクト先を変更できることがあります。同じことはNode.jsなど他のプログラミング言語を使っても実現できますが、Go言語プログラムのCloud Runのコールドスタートは他と比べて体感的に高速な感じがするのでGo言語を使っている次第です。
