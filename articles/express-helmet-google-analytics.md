---
title: "ExpressでHelmetを使うとGoogle Analyticsが動かなくなって困った話"
emoji: "😭"
type: "tech"
topics: ["nodejs", "express", "security"]
published: true
---


# この記事について

この記事では [Express](https://expressjs.com/) のセキュリティ強化ミドルウェアである [Helmet](https://helmetjs.github.io/) を使うとGoogle Analyticsが動かなくなる原因について説明し、対処方法を紹介します。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/db23102d90f6f27a646a9a30a20b4e66) からダウンロード可能です。




# 不具合を再現する手順

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir express-helmet-google-analytics
cd express-helmet-google-analytics
npm init -y
npm install --save dotenv ejs express helmet
touch .env before.js before.ejs
```

エディタで.envを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/db23102d90f6f27a646a9a30a20b4e66?file=.env)

エディタでbefore.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/db23102d90f6f27a646a9a30a20b4e66?file=before.js)

エディタでbefore.ejsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/db23102d90f6f27a646a9a30a20b4e66?file=before.ejs)

ターミナルで下記のコマンドを実行してサーバーを起動します。

```shell
node -r dotenv/config before.js
```

ブラウザで http://localhost:3000/ にアクセスするとコンソールに下記のエラーメッセージが表示されるので確認します。

```
Refused to load the script 'https://www.googletagmanager.com/gtag/js?id=G-1234567890' because it violates the following Content Security Policy directive: "script-src 'self'". Note that 'script-src-elem' was not explicitly set, so 'script-src' is used as a fallback.

localhost/:9 Refused to execute inline script because it violates the following Content Security Policy directive: "script-src 'self'". Either the 'unsafe-inline' keyword, a hash ('sha256-WhkflkrKbEomTZh96OlTs+3mh7cpcaWriwkpSNNkQUo='), or a nonce ('nonce-...') is required to enable inline execution.
```



# エラーの原因

現象についてはエラーメッセージから下記2点を読み取ることができます。

- https://www.googletagmanager.com/gtag/js?id=G-1234567890 の読み込みが拒否された
- インラインでのJavaScript実行が拒否された

原因についてはエラーメッセージからいずれもContent Security Policy（以下「CSP」と呼びます）への違反が理由であることを読み取ることができます。

対処方法についてはこれから紹介する手順によってCSPを適切に設定することでエラーが起こらないようにすることができます。



# 対処方法の手順

下記のコマンドを実行してコーディングの準備をします。

```shell
touch after.js after.ejs
```

エディタでafter.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/db23102d90f6f27a646a9a30a20b4e66?file=after.js)

ポイントを下記に示します。

1. https://www.google-analytics.com などをimg-src、script-src、connect-srcの3つに追加しています。
2. ランダムなnonceを生成してscript-srcに追加しています。

エディタでafter.ejsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/db23102d90f6f27a646a9a30a20b4e66?file=after.ejs)

ポイントを下記に示します。

1. インラインでJavaScriptコードを実行するscriptタグにnonce属性を追加しています。

ターミナルで下記のコマンドを実行してサーバーを起動します。

```shell
node -r dotenv/config after.js
```

ブラウザで http://localhost:3000/ にアクセスしてコンソールにエラーメッセージが表示されないことを確認します。



# 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [CSPが設定されているページでのGoogleタグマネージャーの使用](https://developers.google.com/tag-manager/web/csp)



# おわりに

ExpressでカスタムECシステムを開発している時にデザイナー兼マネージャーさんから「Google Analyticsを追加するのって大変ですか？」と聞かれ「簡単ですよ！」と答えましたが、実際にやってみるとこの不具合に遭遇して結構な時間を溶かしたのも今では良い思い出です。こうやって記事のネタになったので結果オーライですね。この記事をお読みになった方も時間を溶かした思い出がありましたらお気軽にコメントをいただければ幸いです（励みになります）。この記事が同じことで困っている方の助けになれば幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [ExpressでHelmetを使ってContent Security Policyを設定する方法](https://zenn.dev/tatsuyasusukida/articles/express-content-security-policy)
