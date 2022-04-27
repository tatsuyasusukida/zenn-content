---
title: "Auth0で1つのアクセストークンを使って複数のAPIにアクセスする方法"
emoji: "🔑"
type: "tech"
topics: ["nodejs", "auth0"]
published: true
---



# この記事について

この記事では [Auth0](https://auth0.com/) で1つのアクセストークン(JWT) を使ってNode.jsの [Express](https://expressjs.com/) で作った複数のAPIにアクセスする方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. APIの登録
2. APIの設定
3. アプリケーションの登録
4. アプリケーションの設定
5. APIのコーディング
6. UIのコーディング
7. 動作確認

Auth0のAPIやアプリケーションの登録／設定の詳しい操作手順については [Node.jsでAuth0を利用してAPI認可を実装する](https://zenn.dev/tatsuyasusukida/articles/auth0-webapi) を参考にしていただければ幸いです。



# APIの登録

Auth0のWebコンソールのAPIsページで下記のAPIを登録します。

- Name: Auth0 Multiple API
- Identifier: auth0-multiple-api
- Signing Algorithm: RS256



# APIの設定

APIのPermissionsページで下表3点のスコープを追加します。

| Permission | Description |
| ---- | ---- |
| api1:read:status | API1 Read Status |
| api2:read:status | API2 Read Status |
| api3:read:status | API3 Read Status |

また、QuickStartページでNode.jsのタブをクリックして表示されるサンプルコードに含まれる下記3点を後の手順のために控えます。

- jwksUri (例: https://example.jp.auth0.com/.well-known/jwks.json)
- audience (auth0-multiple-api)
- issuer (例: https://example.jp.auth0.com/)



# アプリケーションの登録

Applicationsページで下記のアプリケーションを登録します。

- Name: Auth0 Multiple API Client
- Application Type: Single Page Web Applications



# アプリケーションの設定

ApplicationのSettingsページで下記3点の設定を変更して保存します。

- Allowed Callback URLs: http://localhost:8080/
- Allowed Logout URLs: http://localhost:8080/
- Allowed Web Origins: http://localhost:8080/

また、下記の2点を後の手順のために控えます。

- Domain（例: example.jp.auth0.com）
- ClientId（例: 0123456789abcdefghijklmnopqrstuv）



# APIのコーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir auth0-multiple-api
cd auth0-multiple-api
npm init -y
npm install --save cors dotenv express express-jwt express-jwt-authz http-server jwks-rsa
touch .env api.js
```

## .env

エディタで.envを開いて前の手順で控えた内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/27f303a4bdac46209b513aaf85353bd5?file=.env.example)

## api.js

エディタでapi.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/27f303a4bdac46209b513aaf85353bd5?file=api.js)

ポイントについては [Node.jsでAuth0を利用してAPI認可を実装する](https://zenn.dev/tatsuyasusukida/articles/auth0-webapi#main.js) の記事と同様ですので割愛します。



# UIのコーディング

同じディレクトリまたはuiなどの名前で別のディレクトリに作成して移動してから下記のコマンドを実行します。

```shell
touch auth_config.json index.html ui.js
```

[この記事に関するGist](https://gist.github.com/tatsuyasusukida/27f303a4bdac46209b513aaf85353bd5) ではディレクトリを使用できないので同じディレクトに作成していますが、個人的にはAPIに関係するファイルはapiディレクトリ、UIに関係するファイルはuiディレクトリに分けた方がスッキリすると考えています。

## auth_config.json

エディタでauth_config.jsonを開いて前の手順で控えた内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/27f303a4bdac46209b513aaf85353bd5?file=auth_config.example.json)

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/27f303a4bdac46209b513aaf85353bd5?file=index.html)

## ui.js

エディタでui.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/27f303a4bdac46209b513aaf85353bd5?file=ui.js)

ポイントについては [Node.jsでAuth0を利用してAPI認可を実装する](https://zenn.dev/tatsuyasusukida/articles/auth0-webapi#main.js-1) の記事と同様ですので割愛します。ただし、createAuth0Clientのscopeオプションがapi1:read:status api2:read:statusとなっている点にはご注目ください。



# 動作確認

ターミナルで下記のコマンドを実行してAPIを起動します。

```shell
node -r dotenv/config 
```

新しいターミナルを起動して下記のコマンドを実行してUIを起動します。

```shell
npx http-server -c-1
```

ブラウザで http://localhost:8080 にアクセスしてから「ログイン」ボタンをクリックして認証と認可を行います。

![UIにアクセスした直後の様子です。Webページは認証、API1、API2、API3の4つのセクションで構成されています。認証セクションはログインボタンとログアウトボタンを含んでいます。API1セクションはAPI1のステータスを取得するボタンとレスポンスの表示部を含んでいます。API2とAPI3も同様です。](/images/articles/auth0-multiple-api/check-01.png)

認証と認可が完了したら「API1のステータスを取得する」ボタンをクリックしてAPI1のステータスを取得できることを確認します。

![「API1のステータスを取得する」ボタンをクリックした直後の様子です。レスポンスの表示部の内容がResponse: {"name": "api1", "ok": true}になっています。](/images/articles/auth0-multiple-api/check-02.png)

続いて「API2のステータスを取得する」ボタンをクリックしてAPI2のステータスを取得できることを確認します。

![「API2のステータスを取得する」ボタンをクリックした直後の様子です。レスポンスの表示部の内容がResponse: {"name": "api2", "ok": true}になっています。](/images/articles/auth0-multiple-api/check-03.png)

続いて「API3のステータスを取得する」ボタンをクリックしてAPI3のステータスを取得**できない**ことを確認します（api3:read:statusを認可していないので取得に失敗するのが正しい動作です）。

![「API3のステータスを取得する」ボタンをクリックした直後の様子です。レスポンスの表示部の内容がResponse: Insufficient scopeになっています。](/images/articles/auth0-multiple-api/check-04.png)



# 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [Configure Logical API for Multiple APIs](https://auth0.com/docs/get-started/apis/set-logical-api)



# おわりに

JWTのaud（audience、トークンの受け取り手）はURLである必要があると思い込んでいたのでURLでなくてもOKであることに加え、複数のAPIで使い回しても良いことを知った時は驚きでした。[Auth0のコミュニティのWebページ](https://community.auth0.com/t/my-token-has-multiple-audiences-is-that-normal/41417) によるとAuth0のJWTのaudは2つの要素からなる配列であり、そのうち1件はAuth0のuserinfo（ユーザー情報を取得するためのAPI）なので実質1つのaudienceだけを指定することができます。複数のaudienceを指定できることが理想ですが、Auth0の仕様でできないのであればこの記事で紹介したように方法を使うことで1つのトークンで複数のAPIにアクセスすることができます。もっと良い方法をご存知の方がいましたらお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [Node.jsでAuth0を利用してAPI認可を実装する](https://zenn.dev/tatsuyasusukida/articles/auth0-webapi)
- [Node.jsでAuth0を利用してユーザー認証を実装する](https://zenn.dev/tatsuyasusukida/articles/auth0-webapp)
