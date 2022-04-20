---
title: "Node.jsでAuth0を利用してユーザー認証を実装する"
emoji: "🔑"
type: "tech"
topics: ["nodejs", "auth0"]
published: true
---

# この記事について

この記事ではNode.jsで [Auth0](https://auth0.com/) を使って [Express](https://expressjs.com/) のWebアプリにユーザー認証を実装する方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. アプリケーションの登録
2. アプリケーションの設定
3. コーディング
4. 動作確認



# アプリケーションの登録

Auth0を利用するには事前にアプリケーションを登録する必要があります。
アプリケーションを登録するにはまず [Auth0のWebサイト](https://auth0.com/) にアクセスしてログインします。
なお、Auth0のアカウントを持っていない場合は [サインアップ](https://auth0.com/signup) ページから利用登録をします。

![Auth0のトップページです。ページの右上にLoginリンクがあります。](/images/articles/auth0-webapp/registration-01.png)

Auth0にログインしたらページ左のナビゲーションからApplicationsのページへ移動します。

![Auth0のログイン直後のページです。ページ左の primary navigation から Applications > ApplicationsをクリックすることでApplicationsのページへ移動することができます。](/images/articles/auth0-webapp/registration-02.png)

Applicationsのページへ移動したらページ右上の「Create Application」リンクをクリックします。

![Auth0のApplicationsのページです。ページの右上に「Create Application」リンクが表示されています。](/images/articles/auth0-webapp/registration-03.png)

「Create application」モーダルが表示されたらNameにアプリ名（例：Column Auth0 Web App）を入力してからChoose an application typeとして「Regular Web Applications」を選択し、右下の「Create」ボタンをクリックします。

![Auth0のCreate applicationモーダルです。Nameの入力部やapplication typeの選択部を備えています。](/images/articles/auth0-webapp/registration-04.png)

アプリケーションの登録が完了するとQuick Start（アプリケーションの詳細ページ）が表示されます。

![アプリケーションの登録直後のページです。アプリケーションの詳細ページであり、Quick StartやSettingsなどのタブを備えています。](/images/articles/auth0-webapp/registration-05.png)



# アプリケーションの設定

アプリケーションの登録が完了したので続けてアプリケーションの設定を行います。
アプリケーションの初期設定時はQuick Startを利用するのが便利です。
Auth0のQuick Startは言語×フレームワーク別に用意されているので「Node.js (Express)」を選んでクリックします。

![Auth0のQuick Startです。ApacheやASP.NET (OWIN)など様々な言語×フレームワーク別のQuick Startが一覧表示されています。](/images/articles/auth0-webapp/setting-01.png)

Choose your pathのページが表示されたら左側の「Integrate Now」リンクをクリックします。

![Choose your pathのページです。左側にI want to integrate with my appセクションがあり、右側にI want to explore a sample appセクションがあります。左側のI want to integrate with my appセクションにはIntegrate Nowリンクが含まれています。](/images/articles/auth0-webapp/setting-02.png)

Configure Auth0のページが表示されたらページの下側にある「SAVE SETTINGS AND CONTINUE」ボタンをクリックします。

![Configure Auth0のページです。ページの末尾付近にSAVE SETTINGS AND CONTINUEボタンがあります。](/images/articles/auth0-webapp/setting-03.png)

Integrate Auth0のページが表示されたらConfigure Routerセクションに含まれる下記の3点を後の手順のために控えます。

- baseURL
- issuerBaseURL
- clientID

![Integrate Auth0のページです。Configure RouterセクションにbaseURLやissuerBaseURLなど後の手順で必要となる情報が含まれています。](/images/articles/auth0-webapp/setting-04.png)

secretは下記のコマンドを実行するなどして生成します。

```shell
openssl rand -hex 32
```



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-auth0-webapp
cd column-auth0-webapp
npm init -y
npm install --save dotenv express express-openid-connect
touch main.js
touch .env
```

エディタで.envを開いて先の手順で控えた内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/d3d75c2d7ccce89e30de0bdb9f5dcc4b?file=.env.example)

続いてエディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/d3d75c2d7ccce89e30de0bdb9f5dcc4b?file=main.js)

ポイントを下記に示します。

1. ユーザー認証のミドルウェアを登録しています。必要な設定は環境変数から読み込んでいます。
2. "/"へのGETリクエストに対するハンドラーを登録しています。
3. "/private/"から始まるパスへのリクエストに対するミドルウェアを登録しています。requiresAuthミドルウェアは未認証のリクエストをAuth0のログインページへ転送します。
4. "/private/"へのGETリクエストに対するハンドラーを登録しています。
5. req.oidc.user.subはユーザーを一意に識別するIDとして利用することができます。
6. 環境変数PORTで指定されたポートでリクエストの待ち受けを開始します。



# 動作確認

下記のコマンドを実行してWebサーバーを起動します。

```shell
node -r dotenv/config main.js
```

Webブラウザで http://localhost:3000 にアクセスしてページが表示されることを確認します。

![http://localhost:3000 へのアクセス結果です。ページの内容は {"isPrivate":false} です。](/images/articles/auth0-webapp/check-01.png)

続けてWebブラウザで http://localhost:3000/private/ にアクセスしてAuth0のログインページへ転送されることを確認します。

![Auth0のログインページです。メールアドレスやパスワードを入力するテキストボックスに加え、Googleで続けるボタンなどを備えています。](/images/articles/auth0-webapp/check-02.png)

Googleアカウントを利用するなどしてログインするとページの内容が表示されることを確認します。

![http://localhost:3000 へのアクセス結果です。ページの内容は {"isPrivate":false} です。](/images/articles/auth0-webapp/check-03.png)

ちなみにログアウトするには http://localhost:3000/logout にアクセスします。



# おわりに

Auth0はWebアプリのユーザー認証だけではなく、ネイティブアプリやシングルページWebアプリケーションがバックエンドのAPIにアクセスするための認可のしくみを実装するためにも利用することができます。
詳しい手順については [別の記事](https://zenn.dev/tatsuyasusukida/articles/auth0-webapi) で紹介していますので興味がありましたらご覧いただければ幸いです。
最後まで読んでいただきありがとうございました！
