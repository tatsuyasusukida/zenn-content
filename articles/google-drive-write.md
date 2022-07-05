---
title: "Google Driveにライブラリなしでファイルをアップロードする方法"
emoji: "🏎"
type: "tech"
topics: ["javascript", "google", "googledrive", "oauth"]
published: true
---

## この記事について

この記事ではWebアプリからJavaScriptを使って[Google Drive](https://www.google.com/drive/)にライブラリなしでファイルをアップロードする方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/5fcae93f75a5ccc0e43a1d6a86ef223c)



## おおまかな流れ

おおまかな流れを下記に示します。

1. Google Drive APIの有効化
2. OAuth同意画面の作成
3. 認証情報の作成
4. コーディングの準備
5. コーディング
6. 動作確認

[Google Cloud Platform](https://console.cloud.google.com/)のアカウントが必要ですので、お持ちでない場合は事前にユーザー登録を行います。



## Google Drive APIの有効化

APIとサービスの[Google Drive API](https://console.cloud.google.com/apis/library/drive.googleapis.com)のページにアクセスしてからAPIを有効化します。

### 参考画像

![](/images/articles/google-drive-write/img-api-01.jpg)

![](/images/articles/google-drive-write/img-api-02.jpg)

![](/images/articles/google-drive-write/img-api-03.jpg)

![](/images/articles/google-drive-write/img-api-04.jpg)

![](/images/articles/google-drive-write/img-api-05.jpg)

![](/images/articles/google-drive-write/img-api-06.jpg)



## OAuth同意画面の作成

APIとサービスの[OAuth同意画面](https://console.cloud.google.com/apis/credentials/consent)のページにアクセスして下記の内容を入力してから「保存して次へ」ボタンをクリックします。

- アプリ名｜例: Drive Write
- ユーザーサポートメール
- デベロッパーの連絡先情報

なお、アプリ名に「Google」などの商標が含まれているとエラーメッセージが表示されて次へ進めないので留意します（ただ「Google Drive OAuth」とかは大丈夫なんですよね、何でだろう...）。

続いてスコープの画面で下記のスコープを追加してから「保存して次へ」ボタンをクリックします。

- https://www.googleapis.com/auth/drive.file

続いてテストユーザーの画面でご自身のGoogleアカウントのメールアドレスを追加してから「保存して次へ」ボタンをクリックします。

### 参考画像

![](/images/articles/google-drive-write/img-oauth-01.jpg)

![](/images/articles/google-drive-write/img-oauth-02.jpg)

![](/images/articles/google-drive-write/img-oauth-03.jpg)

![](/images/articles/google-drive-write/img-oauth-04.jpg)

![](/images/articles/google-drive-write/img-oauth-05.jpg)

![](/images/articles/google-drive-write/img-oauth-06.jpg)

![](/images/articles/google-drive-write/img-oauth-07.jpg)

![](/images/articles/google-drive-write/img-oauth-08.jpg)



## 認証情報の作成

APIとサービスの[認証情報](https://console.cloud.google.com/apis/credentials)のページにアクセスして下記の内容を入力してから「認証情報を作成 > OAuth クライアント ID」メニューをクリックします。

OAuth クライアント IDの作成のページが表示されたらアプリケーションの種類として「ウェブ アプリケーション」を選んでから作成ボタンをクリックします。

認証情報の一覧ページが表示されたら作成されたOAuth 2.0 クライアントIDの「編集」ボタンをクリックして下記の2点を追加します。

- 承認済みの JavaScript 生成元: http://localhost:8080
- 承認済みのリダイレクト URI: http://localhost:8080/

作成されたOAuth 2.0 クライアントIDを後の手順のためにコピーします。

### 参考画像

![](/images/articles/google-drive-write/img-client-01.jpg)

![](/images/articles/google-drive-write/img-client-02.jpg)

![](/images/articles/google-drive-write/img-client-03.jpg)

![](/images/articles/google-drive-write/img-client-04.jpg)

![](/images/articles/google-drive-write/img-client-05.jpg)

![](/images/articles/google-drive-write/img-client-06.jpg)

![](/images/articles/google-drive-write/img-client-07.jpg)

![](/images/articles/google-drive-write/img-client-08.jpg)



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir google-drive-write
cd google-drive-write
npm init -y
npm install --save-dev http-server
touch config.mjs index.html main.mjs signin.mjs upload.mjs
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### index.html

@[gist](https://gist.github.com/tatsuyasusukida/5fcae93f75a5ccc0e43a1d6a86ef223c?file=index.html)

### main.mjs

@[gist](https://gist.github.com/tatsuyasusukida/5fcae93f75a5ccc0e43a1d6a86ef223c?file=main.mjs)

### config.mjs

@[gist](https://gist.github.com/tatsuyasusukida/5fcae93f75a5ccc0e43a1d6a86ef223c?file=config.example.mjs)

clientIdには先の手順でコピーしたOAuth 2.0 クライアントIDをペーストします。

### signin.mjs

@[gist](https://gist.github.com/tatsuyasusukida/5fcae93f75a5ccc0e43a1d6a86ef223c?file=signin.mjs)

詳細についてはGoogle Identity Platformの[クライアントサイド ウェブ アプリケーション用の OAuth 2.0](https://developers.google.com/identity/protocols/oauth2/javascript-implicit-flow)のページが参考になります。

### upload.mjs

@[gist](https://gist.github.com/tatsuyasusukida/5fcae93f75a5ccc0e43a1d6a86ef223c?file=upload.mjs)

詳細についてはGoogle Drive for Developmersの[Upload file data](https://developers.google.com/drive/api/guides/manage-uploads)のページが参考になります。




## 動作確認

下記のコマンドを実行してWebサーバーを起動します。なお -c-1 オプションはキャッシュを無効にするために指定しています。

```shell
npx http-server -c-1
```

ブラウザで http://localhost:8080/ にアクセスします。

「Sign in」ボタンをクリックするとGoogleのログインページが表示されるのでテストユーザーとして登録したアカウントでログインします。

「Upload」ボタンをクリックします。

[Google Drive](https://drive.google.com/drive/)にアクセスして名称が「test-google-drive-write」であるテキストファイルが作成されていることを確認します。

### 参考画像

![](/images/articles/google-drive-write/img-check-01.png)

![](/images/articles/google-drive-write/img-check-02.png)




## おわりに

Google DriveのAPIはシンプルなのでライブラリなしでも簡単に利用することができます。

FitbitアプリやシングルページのWebアプリを作成しているとアプリデータをどこに保存すればよいか悩むことがあります。Firebaseや自前のバックエンドでもよいですが、単純にデータを読み書きするだけであればGoogle Driveも有力な選択肢の一つだと思いました。
