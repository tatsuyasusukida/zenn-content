---
title: "ExpressでHelmet.jsを使ってContent Security Policyを設定する方法"
emoji: "🔒"
type: "tech"
topics: ["nodejs", "express", "security"]
published: true
---



# この記事について

この記事では [Express](https://expressjs.com/) のセキュリティ強化ミドルウェアである [Helmet.js](https://helmetjs.github.io/) を使って [Content Security Policy](https://developer.mozilla.org/docs/Web/HTTP/CSP)（以下「CSP」と呼びます）を設定する方法について紹介します。題材として [GoogleタグマネージャーのCSP](https://developers.google.com/tag-manager/web/csp)を参考にしてGoogle Analyticsを利用できるCSPを設定します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認

コーディングについては [この記事のGistページ]() からソースコードをダウンロード可能です。



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir express-content-security-policy
cd express-content-security-policy
npm init -y
npm install --save express helmet
touch main.js
```

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/c6e704519e451933e65a80dadc345d2c?file=main.js)

ポイントを下記に示します。

1. helmetミドルウェアを登録する時にcontentSecurityPolicyオプションを使ってCSPを設定しています。
2. directivesをお忘れなく（私はよく忘れます）。デフォルトの設定内容についてはnode_modules/helmet/dist/cjs/index.jsなどからコピー＆ペーストすることができます。
3. img-srcにGoogle AnalyticsのURLを追加しています。
4. script-srcにGoogle AnalyticsのURL2件を追加しています。
5. connect-srcにGoogle AnalyticsのURLを追加しています。



# 動作確認

ターミナルで下記のコマンドを実行してWebサーバーを起動します。

```shell
node main.js
```

新しいターミナルを起動してから下記のコマンドを実行してHTTPリクエストを送信します。

```shell
curl -v http://localhost:3000/
```

実行結果を下記に示します。

```
*   Trying ::1:3000...
* Connected to localhost (::1) port 3000 (#0)
> GET / HTTP/1.1
> Host: localhost:3000
> User-Agent: curl/7.77.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Content-Security-Policy: default-src 'self';base-uri 'self';block-all-mixed-content;font-src 'self' https: data:;form-action 'self';frame-ancestors 'self';img-src 'self' data: https://www.google-analytics.com;object-src 'none';script-src 'self' https://www.google-analytics.com https://ssl.google-analytics.com;script-src-attr 'none';style-src 'self' https: 'unsafe-inline';upgrade-insecure-requests;connect-src 'self' https://www.google-analytics.com
< Cross-Origin-Embedder-Policy: require-corp
< Cross-Origin-Opener-Policy: same-origin
< Cross-Origin-Resource-Policy: same-origin
< X-DNS-Prefetch-Control: off
< Expect-CT: max-age=0
< X-Frame-Options: SAMEORIGIN
< Strict-Transport-Security: max-age=15552000; includeSubDomains
< X-Download-Options: noopen
< X-Content-Type-Options: nosniff
< Origin-Agent-Cluster: ?1
< X-Permitted-Cross-Domain-Policies: none
< Referrer-Policy: no-referrer
< X-XSS-Protection: 0
< Content-Type: application/json; charset=utf-8
< Content-Length: 11
< ETag: W/"b-Ai2R8hgEarLmHKwesT1qcY913ys"
< Date: Wed, 27 Apr 2022 02:15:18 GMT
< Connection: keep-alive
< Keep-Alive: timeout=5
< 
* Connection #0 to host localhost left intact
{"ok":true}
```

HTTPレスポンスに含まれるContent-Security-Policyヘッダーを見て設定が反映されていることを確認します。



# おわりに

セキュリティを強化する上でContent-Security-Poclicyはとても有用ですが、設定が適切でなければ画像の読み込みに失敗したり、JavaScriptの実行が失敗したりとWebページの不具合につながります。Helmet.jsは後付けで追加も可能ですが、早期に追加してCSPなどが有効な状態で開発を進めた方がバグを早めに発見できて良いと考えています。この記事がCSPでGoogle Analyticsなどが動かなくて困っている方の役に立てば幸いです（ちなみにGoogle Analyticsが機能するためにはnonceの設定も行う必要があります）。最後までお読みいただきありがとうございました！
