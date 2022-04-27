---
title: "Node.jsでAuth0を利用してAPI認可を実装する"
emoji: "🔑"
type: "tech"
topics: ["nodejs", "auth0"]
published: true
---

# この記事について

この記事ではNode.jsで [Auth0](https://auth0.com/) を使って [Express](https://expressjs.com/) のバックエンドにAPI認可（OAuth 2.0）を実装する方法について紹介します。
また、動作確認のために [auth0-spa-js](https://github.com/auth0/auth0-spa-js) を使って下記4点の機能を備えたユーザーインタフェース（UI）をシングルページWebアプリケーションとして作成します。

- ログイン
- ログアウト
- アクセストークン取得
- バックエンドへのAPIアクセス



# おおまかな手順

おおまかな手順を下記に示します。

1. APIの登録
2. APIの設定
3. アプリケーションの登録
4. アプリケーションの設定
5. APIのコーディング
6. UIのコーディング
7. 動作確認



# APIの登録

Auth0を利用するにはAPIに加え、APIにアクセスするアプリケーションの両方を事前に登録する必要があります。
まずAPIを登録する手順について紹介します。
APIを登録するにはまず [Auth0のWebサイト](https://auth0.com/) にアクセスしてログインします。
なお、Auth0のアカウントを持っていない場合は [サインアップ](https://auth0.com/signup) ページから利用登録をします。

![Auth0のトップページです。ページの右上にLoginリンクがあります。](/images/articles/auth0-webapi/api-registration-01.png)

Auth0にログインしたらページ左のナビゲーションからAPIsのページへ移動します。

![Auth0のログイン直後のページです。ページ左の primary navigation から Applications > APIsをクリックすることでAPIsのページへ移動することができます。](/images/articles/auth0-webapi/api-registration-02.png)

APIsのページへ移動したらページ右上の「Create API」リンクをクリックします。

![Auth0のAPIsのページです。ページの右上に「Create API」リンクが表示されています。](/images/articles/auth0-webapi/api-registration-03.png)

「New API」モーダルが表示されたら下記の内容を入力／選択してから右下の「Create」ボタンをクリックします。

- Name: API名（例：Column Auth0 Web API）
- Identifier: APIの識別子（例：column-auth0-webapi）
- Signing Algorithm: RS256

![Auth0のNew APIモーダルです。Nameの入力部やIdentifierの入力部などを備えています。](/images/articles/auth0-webapi/api-registration-04.png)

APIの登録が完了するとQuick Start（APIの詳細ページ）が表示されます。

![APIの登録直後のページです。APIの詳細ページであり、Quick StartやSettingsなどのタブを備えています。](/images/articles/auth0-webapi/api-registration-05.png)



# APIの設定

APIの登録が完了したので続けてAPIの設定を行います。
Quick Startのページの「2. Configuring your API to accept RS256 signed tokens」セクションに含まれるC♯、Node.js、PHPのタブの中から「Node.js」を選んでクリックすると、Node.jsのソースコードのサンプルが表示されるので下記3点の情報を後の手順のために控えます。

- jwksUri
- audience
- issuer

![APIのQuick Startのページの「2. Configuring your API to accept RS256 signed tokens」セクションに含まれるNode.jsのサンプルコードです。jwksUri、audience、issuerなど後の手順で必要となる情報が含まれています。](/images/articles/auth0-webapi/api-setting-01.png)

続いてページの先頭に戻ってから「Permissions」タブをクリックすると「Add a Permission (Scope)」のフォームが表示されるので下記の内容を入力してから「Add」ボタンをクリックします。

- Permission (Scope): read:status
- Description: Read status

![APIのPermissionsページです。権限を追加するためのAdd a Permission (Scope)を備えています。](/images/articles/auth0-webapi/api-setting-02.png)



# アプリケーションの登録

APIの登録と設定が完了したので続けてアプリケーションの登録を行います。
ページ左のナビゲーションからApplicationsのページへ移動します。

![Auth0のダッシュボードで primary navigation の Applications グループを展開している様子です。ApplicationsグループにはApplicationsリンクが含まれており、ApplicationsリンクをクリックしてApplicationsのページに移動することができます](/images/articles/auth0-webapi/app-registration-01.png)

Applicationsのページへ移動したらページ右上の「Create Application」リンクをクリックします。

![Auth0のApplicationsのページです。ページの右上に「Create Application」リンクが表示されています。](/images/articles/auth0-webapi/app-registration-02.png)

「Create application」モーダルが表示されたら下記の内容を入力／選択してから右下の「Create」ボタンをクリックします。

- Name: アプリ名（例：Column Auth0 Web API Client）
- Choose an application type: Single Page Web Application

![Auth0のApplicationsページのCreate applicationモーダルです。Nameの入力部やapplication typeの選択部を備えています。](/images/articles/auth0-webapi/app-registration-03.png)

アプリケーションの登録が完了するとQuick Start（アプリケーションの詳細ページ）が表示されます。

![Auth0のアプリケーションの詳細ページです。Quick StartやSettingsなどのタブを備えています。](/images/articles/auth0-webapi/app-registration-04.png)



# アプリケーションの設定

アプリケーションの登録が完了したので続けてアプリケーションの設定を行います。
アプリケーションの初期設定時はQuick Startを利用するのが便利です。
Quick Startはフレームワーク別に用意されているので「JavaScript」を選んでクリックします。

![Auth0のQuick Startです。Angular、JavaScript、React、VueのQuick Startへのリンクが一覧表示されています。](/images/articles/auth0-webapi/app-setting-01.png)

JavaScriptのQuick Startのページが表示されたら「Configure credentials」セクションまでスクロールして下記2点の情報を後の手順のために控えます。

- domain
- clientId

![Auth0のJavaScriptのQuick StartページのConfigure credentialsセクションです。後の手順で必要となるdomainとclientIdの2点の情報が含まれています。](/images/articles/auth0-webapi/app-setting-02.png)

続いてページの先頭に戻ってから「Settings」タブをクリックすると設定フォームが表示されるので下記3点の内容を入力します（いずれも「Application URIs」セクションに含まれています）。

- Allowed Callback URLs: http://localhost:8080
- Allowed Logout URLs: http://localhost:8080
- Allowed Web Origins: http://localhost:8080

![Auth0のアプリケーションの設定ページのApplication URIsセクションです。Allowed Callback URLsの入力部やAllowed Logout URLsの入力部が含まれています。](/images/articles/auth0-webapi/app-setting-03.png)

入力が終わったらページの最後の方にある「Save Changes」ボタンをクリックします。

![Auth0のアプリケーションの設定ページの最後の方にある「Save Changes」ボタンです。](/images/articles/auth0-webapi/app-setting-04.png)

以上でAuth0のAPIとアプリケーションの登録が完了しました。
あとは楽しいコーディングだけです。



# APIのコーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-auth0-webapi
cd column-auth0-webapi
npm init -y
npm install --save cors dotenv express express-jwt@6 express-jwt-authz jwks-rsa
touch .env
touch main.js
```

## .env

エディタで.envを開いて先の手順で控えた内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/099b4ee0a22bb3d100f7134f4e260320?file=.env.example)

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/099b4ee0a22bb3d100f7134f4e260320?file=main.js)

ポイントを下記に示します。

1. [CORS](https://developer.mozilla.org/ja/docs/Web/HTTP/CORS) を許可するためのミドルウェアを登録しています。
2. 認証のためのミドルウェアを登録しています。
3. "/status"へのGETリクエストのハンドラを登録しています。jwtAuthzのミドルウェアを使ってread:statusが許可されているかどうかを検証しています。
4. リクエスト処理中にエラーが発生した時のハンドラを登録しています。
5. 環境変数PORTで指定されたポートでリクエストの待ち受けを開始します。

下記のコマンドを実行してAPI（バックエンド）を起動します。

```shell
node -r dotenv/config main.js
```



# UIのコーディング

新しいターミナルを開いてから下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-auth0-webapi-client
cd column-auth0-webapi-client
touch auth_config.json
touch index.html
touch main.js
```

## auth_config.json

エディタでauth_config.jsonを開いて先の手順で控えた内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ce1b2533d3fbd38b5e3582fe0217a049?file=auth_config.json.example)

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ce1b2533d3fbd38b5e3582fe0217a049?file=index.html)

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/ce1b2533d3fbd38b5e3582fe0217a049?file=main.js)

ポイントを下記に示します。

1. auth_config.jsonから設定を取得しています。
2. auth0のクライアントを生成しています。
3. 認証が完了しているかどうかを確認しています。
4. 認証が完了していない場合はクエリ文字列を確認してcodeとstateの両方が含まれる場合はhandleRedirectCallbackメソッドを呼び出して認証を完了させます。
5. HTMLページの要素を取得しています。
6. ログインボタンがクリックされた時のハンドラを登録しています。
7. ログアウトボタンがクリックされた時のハンドラを登録しています。
8. ステータス取得ボタンがクリックされた時のハンドラを登録しています。
9. getTokenSilentlyメソッドを呼び出してアクセストークンを取得しています。
10. 取得したアクセストークンをAuthorizationヘッダーに設定しています。
11. 認証が完了しているかどうかに基づいてボタンの有効／無効を切り替えています。

下記のコマンドを実行してUI（フロントエンド）を起動します。

```shell
npx http-server -c-1
```

なお-c-1はキャッシュを無効にするためのパラメーターであり、トライ＆エラーを繰り返すときに変更が反映されないことを防ぐために設定しています。



# 動作確認

Webブラウザを開いて http://localhost:8080 にアクセスします。

![http://localhost:8080にアクセスした様子です。ログイン、ログアウト、ステータス取得の3つのボタンが表示されています。](/images/articles/auth0-webapi/check-01.png)

ログインボタンをクリックするとAuth0のログインページへ移動します。

![ログインボタンをクリックした直後の様子です。Auth0のログインページが表示されています。](/images/articles/auth0-webapi/check-02.png)

Googleアカウントを利用するなどしてログインするとアプリの認可ページ（Authorize App）が表示されるので「Accept」をクリックしてアプリを認可します。

![Auth0の認可ページです。アプリが要求しているスコープ（ProfileやStatus）に関する説明に加え、DeclineボタンとAcceptボタンを備えています](/images/articles/auth0-webapi/check-03.png)

アプリの認可が完了するとアプリのトップページに戻ります。
この際、ログインが完了しているのでログインボタンが無効になり、ログアウトボタンとステータス取得ボタンが有効になっています。

![ログインが完了した直後の様子です。ログインボタンが無効になり、ログアウトボタンとステータス取得ボタンが有効になっています。](/images/articles/auth0-webapi/check-04.png)

ステータス取得ボタンをクリックしてバックエンドへのリクエストが成功し、メッセージが表示されることを確認します。

![ステータス取得ボタンをクリックした直後の様子です。3つのボタンの下にResponse: {"ok": true}と表示されています。](/images/articles/auth0-webapi/check-05.png)



# 参考にしたWebページ

記事を作成する上で参考にしたWebページを下記に示します。

- [Server Applications with API](https://auth0.com/docs/get-started/architecture-scenarios/server-application-api)
- [Auth0 と auth0-spa-js を使った独自Backend APIのアクセス制御方法の整理](https://zenn.dev/corocn/articles/374b8695da9411)



# おわりに

Auth0はAPI認可だけではなく、Webアプリにユーザー認証を実装するためにも利用することができます。
詳しい手順については [別の記事](https://zenn.dev/tatsuyasusukida/articles/auth0-webapp) で紹介していますので興味がありましたらご覧いただければ幸いです。
最後までお読みいただきありがとうございました。



# 改訂記録

## 2022年4月27日

1. `npm install cors` が抜けていたので追加しました。
2. `npm install express-jwt` を実行するとメジャーバージョンが7のnpmパッケージがインストールされてエラーが発生するのでインストール時に@6を末尾に追加してバージョンを指定しました。

変更前のnpm installコマンドは下記の通りです。

```
npm install --save dotenv express express-jwt express-jwt-authz jwks-rsa
```

変更後のnpm installコマンドは下記の通りです。

```
npm install --save cors dotenv express express-jwt@6 express-jwt-authz jwks-rsa
```
