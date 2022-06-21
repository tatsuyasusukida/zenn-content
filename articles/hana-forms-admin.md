---
title: "Googleフォームの管理ページのようなものは小一時間では作れない【デモ動画あり】"
emoji: "🌷"
type: "tech"
topics: ["nodejs"]
published: true
---

## この記事について

この記事では [Google Forms](https://www.google.com/forms/about/) のようなオンラインフォーム作成ツールの管理ページを2〜3時間（コード行数の合計 = 2,190行）で作成する手順について説明します。この記事で実装するオンラインフォーム作成ツールは下記の2つのサブシステムから構成されます。

1. ユーザーが回答を入力するためのサブシステム
2. ユーザーがフォームを管理するためのサブシステム

この記事では後者の「2. ユーザーがフォームを管理するためのサブシステム」を対象として説明します。前者の「1. ユーザーが回答を入力するためのサブシステム」については [Googleフォームの回答ページのようなものを小一時間で作る](https://zenn.dev/tatsuyasusukida/articles/hana-forms-public) の記事をご参照ください。この記事の関連リソースを下記に示します。

- [デモ](https://hana-forms-admin-6fsrjyleha-an.a.run.app/)
- [デモ動画](https://www.youtube.com/watch?v=P0CTjanohtY)
- [ソースコード](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182#file-api-answer-submit-delete-js)

https://www.youtube.com/watch?v=P0CTjanohtY



## おおまかな流れ

おおまかな流れを下記に示します。

1. データベースの準備
2. コーディングの準備
3. コーディング
4. 動作確認



## データベースの準備

下記のSQL文を発行してデータベースの準備をします。

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=db.sql)

ターミナルからSQL文を発行するには下記のコマンドを実行します。なお、パスワードを設定していない場合は-pオプションは不要です。

```shell
mysql -u root -p 
```



## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir hana-forms-admin
cd hana-forms-admin
npm init -y
npm install --save dotenv ejs express express-openid-connect morgan mysql2 nocache sequelize
touch .env api-answer-submit-delete.js api-form-initialize-add.js api-form-initialize-edit.js api-form-submit-add.js api-form-submit-delete.js api-form-submit-edit.js api-form-validate.js api-item-initialize-add.js api-item-initialize-edit.js api-item-submit-add.js api-item-submit-delete.js api-item-submit-edit.js api-item-validate.js api-option-initialize-add.js api-option-initialize-edit.js api-option-submit-add.js api-option-submit-delete.js api-option-submit-edit.js api-option-validate.js find-answer.js find-form.js find-item.js find-option.js fixture.js form.js main.js model.js render-answer-detail.js render-form-detail.js render-form-list.js render-item-detail.js render-option-detail.js static-js-app.js static-js-ui-delete.js static-js-ui-form.js validate-form.js validate-item.js validate-option.js validate.js view-answer-delete.ejs view-answer-detail.ejs view-form-add.ejs view-form-delete.ejs view-form-detail.ejs view-form-edit.ejs view-form-list.ejs view-home.ejs view-item-add.ejs view-item-delete.ejs view-item-detail.ejs view-item-edit.ejs view-layout-footer.ejs view-layout-header.ejs view-option-add.ejs view-option-delete.ejs view-option-detail.ejs view-option-edit.ejs view-partial-form.ejs view-partial-item.ejs view-partial-option.ejs
```



## コーディング

ソースコードの一覧を下記に示します。

- [api-answer-submit-delete.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-answer-submit-delete-js)
- [api-form-initialize-add.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-form-initialize-add-js)
- [api-form-initialize-edit.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-form-initialize-edit-js)
- [api-form-submit-add.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-form-submit-add-js)
- [api-form-submit-delete.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-form-submit-delete-js)
- [api-form-submit-edit.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-form-submit-edit-js)
- [api-form-validate.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-form-validate-js)
- [api-item-initialize-add.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-item-initialize-add-js)
- [api-item-initialize-edit.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-item-initialize-edit-js)
- [api-item-submit-add.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-item-submit-add-js)
- [api-item-submit-delete.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-item-submit-delete-js)
- [api-item-submit-edit.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-item-submit-edit-js)
- [api-item-validate.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-item-validate-js)
- [api-option-initialize-add.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-option-initialize-add-js)
- [api-option-initialize-edit.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-option-initialize-edit-js)
- [api-option-submit-add.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-option-submit-add-js)
- [api-option-submit-delete.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-option-submit-delete-js)
- [api-option-submit-edit.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-option-submit-edit-js)
- [api-option-validate.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-api-option-validate-js)
- [find-answer.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-find-answer-js)
- [find-form.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-find-form-js)
- [find-item.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-find-item-js)
- [find-option.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-find-option-js)
- [fixture.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-fixture-js)
- [form.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-form-js)
- [main.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-main-js)
- [model.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-model-js)
- [render-answer-detail.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-render-answer-detail-js)
- [render-form-detail.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-render-form-detail-js)
- [render-form-list.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-render-form-list-js)
- [render-item-detail.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-render-item-detail-js)
- [render-option-detail.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-render-option-detail-js)
- [static-js-app.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-static-js-app-js)
- [static-js-ui-delete.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-static-js-ui-delete-js)
- [static-js-ui-form.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-static-js-ui-form-js)
- [validate-form.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-validate-form-js)
- [validate-item.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-validate-item-js)
- [validate-option.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-validate-option-js)
- [validate.js](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-validate-js)
- [view-answer-delete.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-answer-delete-ejs)
- [view-answer-detail.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-answer-detail-ejs)
- [view-form-add.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-form-add-ejs)
- [view-form-delete.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-form-delete-ejs)
- [view-form-detail.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-form-detail-ejs)
- [view-form-edit.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-form-edit-ejs)
- [view-form-list.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-form-list-ejs)
- [view-home.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-home-ejs)
- [view-item-add.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-item-add-ejs)
- [view-item-delete.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-item-delete-ejs)
- [view-item-detail.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-item-detail-ejs)
- [view-item-edit.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-item-edit-ejs)
- [view-layout-footer.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-layout-footer-ejs)
- [view-layout-header.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-layout-header-ejs)
- [view-option-add.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-option-add-ejs)
- [view-option-delete.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-option-delete-ejs)
- [view-option-detail.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-option-detail-ejs)
- [view-option-edit.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-option-edit-ejs)
- [view-partial-form.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-partial-form-ejs)
- [view-partial-item.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-partial-item-ejs)
- [view-partial-option.ejs](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file-view-partial-option-ejs)

主要なソースコードの内容を下記に示します。

### model.js

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=model.js)

### main.js

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=main.js)

### view-form-add.ejs

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=view-form-add.ejs)

### view-partial-form.ejs

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=view-partial-form.ejs)

### static-js-ui-form.js

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=static-js-ui-form.js)

### api-form-initialize-add.js

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=api-form-initialize-add.js)

### api-form-validate.js

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=api-form-validate.js)

### api-form-submit-add.js

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=api-form-submit-add.js)

### api-form-submit-add.js

@[gist](https://gist.github.com/tatsuyasusukida/d113b27ac717f7c65f36f138a0d49182?file=api-form-submit-add.js)



## 動作確認

下記のコマンドを実行してデータベースにテーブルを作成してレコードを挿入します。

```shell
node -r dotenv/config fixture.js
```

下記のコマンドを実行してサーバーを起動します。

```shell
node -r dotenv/config main.js
```

ブラウザで http://localhost:3001/ にアクセスします。

ログインページが表示されたら「Continue with Google」ボタンをクリックするなどしてログインします。

トップページが表示されたら「Forms」リンクをクリックします。

フォームの一覧ページが表示されたら「Add」リンクをクリックします。

フォームの登録ページが表示されたらフォームの内容を入力してから「Add」ボタンをクリックします。

フォームの詳細ページが表示されたら「Add item」リンクをクリックします。

フォーム項目の登録ページが表示されたらフォームの内容を入力してから「Add」ボタンをクリックします。

フォーム項目の詳細ページが表示されたら「Back」リンクをクリックします。

フォームの詳細ページが表示されたら回答ページのURLをクリックします。

回答ページが表示されたらフォームの内容を入力してから「Submit」ボタンをクリックします。

完了ページが表示されたらフォームの詳細ページに戻ってからページをリロードします。

フォームの詳細ページのリロードが完了したら回答リンクをクリックします。

回答の詳細ページが表示されたら回答内容が表示されることを確認します。



### 参考画像

![](/images/articles/hana-forms-admin/img-check-01.jpg)

![](/images/articles/hana-forms-admin/img-check-02.jpg)

![](/images/articles/hana-forms-admin/img-check-03.jpg)

![](/images/articles/hana-forms-admin/img-check-04.jpg)

![](/images/articles/hana-forms-admin/img-check-05.jpg)

![](/images/articles/hana-forms-admin/img-check-06.jpg)

![](/images/articles/hana-forms-admin/img-check-07.jpg)

![](/images/articles/hana-forms-admin/img-check-08.jpg)

![](/images/articles/hana-forms-admin/img-check-09.jpg)



## おわりに

記事とはあまり関係ないのですが、Gistで管理できるようにディレクトリを一切作成せずにコーディングをしましたが、ファイル数が60点にもなるとさすがにディレクトリがないときついですね。ソースコードが短い場合はGistが便利ですが、ソースコードが長い場合は素直にGitHubで管理して必要に応じて引用したい箇所をGistに手動でアップロードするという運用方法の方が適していると感じました。
