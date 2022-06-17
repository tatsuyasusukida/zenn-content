---
title: "Googleフォームの回答ページのようなものを小一時間で作る"
emoji: "🌻"
type: "tech"
topics: ["nodejs"]
published: true
---

## この記事について

この記事では [Google Forms](https://www.google.com/forms/about/) のようなオンラインフォーム作成ツールの回答ページを小一時間（コード行数の合計 = 664行）で作成する手順について説明します。この記事で実装するオンラインフォーム作成ツールは下記の2つのサブシステムから構成されます。

1. ユーザーが回答を入力するためのサブシステム
2. ユーザーがフォームを管理するためのサブシステム

この記事では前者の「1. ユーザーが回答を入力するためのサブシステム」を対象として説明します。関連リソースを下記に示します。

- [デモ](https://hana-forms-6fsrjyleha-an.a.run.app/form/1234abcd/)
- [デモ動画](https://www.youtube.com/watch?v=5lL0_Q22EVw)
- [ソースコード](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-api-initialize-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf)

https://www.youtube.com/watch?v=5lL0_Q22EVw



## おおまかな流れ

おおまかな流れを下記に示します。

1. データベースの準備
2. コーディングの準備
3. コーディング
4. 動作確認



## データベースの準備

下記のSQL文を発行してデータベースの準備をします。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=db.sql)

ターミナルからSQL文を発行するには下記のコマンドを実行します。なお、パスワードを設定していない場合は-pオプションは不要です。

```shell
mysql -u root -p 
```



## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir HanaForms
cd HanaForms
npm init -y
npm install --save dotenv ejs express morgan mysql2 nocache sequelize
touch .env api-initialize.js api-validate.js api-submit.js find-form.js fixture.js main.js model.js partial-checkbox.ejs partial-input.ejs partial-radio.ejs partial-textarea.ejs static-js-input.js validate.js view-finish.ejs view-home.ejs view-input.ejs
```



## コーディング

ソースコードの一覧を下記に示します。

- [.env](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-env-example)
- [main.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-main-js)
- [view-home.ejs](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-view-home-ejs)
- [view-input.ejs](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-view-input-ejs)
- [view-finish.ejs](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-view-finish-ejs)
- [partial-input.ejs](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-partial-input-ejs)
- [partial-textarea.ejs](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-partial-textarea-ejs)
- [partial-radio.ejs](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-partial-radio-ejs)
- [partial-checkbox.ejs](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-partial-checkbox-ejs)
- [model.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-model-js)
- [fixture.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-fixture-js)
- [static-js-input.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-static-js-input-js)
- [find-form.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-find-form-js)
- [validate.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-validate-js)
- [api-initialize.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-api-initialize-js)
- [api-validate.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-api-validate-js)
- [api-submit.js](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-api-submit-js)

主要なソースコードの内容を下記に示します。

### model.js

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=model.js)

### main.js

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=main.js)

### view-input.ejs

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=view-input.ejs)

### static-js-input.js

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=static-js-input.js)

### api-initialize.js

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=api-initialize.js)

### api-validate.js

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=api-validate.js)

### api-submit.js

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=api-submit.js)





## 動作確認

下記のコマンドを実行してデータベースにテーブルを作成してレコードを挿入します。

```shell
node -r dotenv/config fixture.js
```

下記のコマンドを実行してサーバーを起動します。

```shell
node -r dotenv/config main.js
```

ブラウザで http://localhost:3000/form/1234abcd/ にアクセスします。

フォームに内容を入力してから送信ボタンをクリックします。

完了ページが表示されることを確認します。

### 参考画像

![](/images/articles/hana-forms-public/img-check-01.jpg)

![](/images/articles/hana-forms-public/img-check-02.jpg)

![](/images/articles/hana-forms-public/img-check-03.jpg)

![](/images/articles/hana-forms-public/img-check-04.jpg)



## おわりに

この記事で作成したサブシステムはチュートリアル用に簡略化されており、さらにGoogle Formsに近づけるためには下記のような機能を設ける必要があると考えています。

- 画像や動画の表示
- ファイルのアップロード
- 受付メールの送信
- テーマの設定

機能の追加に加え、下記のような点に取り組む必要があります。

- ユーザー体験の向上
- セキュリティの向上
- 運用性の向上
- アクセシビリティの向上

この記事を投稿する前に本テーマに関する素晴らしい記事を見つけたので下記にリンクを記載します。

https://zenn.dev/d0ne1s/articles/6a223e6b1a1a36

ユーザーがフォームを管理するためのサブシステムを作成する手順については来週中を目標に記事にまとめて投稿しようと思います。
