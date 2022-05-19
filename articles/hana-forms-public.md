---
title: "小一時間でGoogleフォームを作る"
emoji: "🌻"
type: "tech"
topics: ["nodejs"]
published: false
---

## この記事について

この記事では [Google Forms](https://www.google.com/forms/about/) のようなオンラインフォーム作成ツールをNode.jsで作る方法について紹介します。この記事で実装するオンラインフォーム作成ツールは下記の2つのサブシステムから構成されます。

- Hana Forms Public: ユーザーが回答を入力するためのサブシステム
- Hana Forms Admin: ユーザーがフォームを管理するためのサブシステム

この記事では前者のHana Forms Publicを対象とします。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf#file-api-initialize-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf)

ちなみにHanaは私の娘の名前です :) 



## おおまかな流れ

おおまかな流れを下記に示します。

1. データベースの準備
2. コーディングの準備
3. コーディング
4. 動作確認



## データベースの準備

下記のコマンドを実行してデータベースの準備をします。

```shell
create database hana_forms charset utf8;
create user hana_forms@localhost identified by 'password';
grant all privileges on hana_forms.* to hana_forms@localhost;
```



## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir HanaForms
cd HanaForms
npm init -y
npm install --save dotenv ejs express morgan mysql2 sequelize
touch .env api-initialize.js api-validate.js api-submit.js fixture.js main.js model.js static-js-input.js validate.js view-finish.ejs view-home.ejs view-input.ejs
```



## コーディング

### .env

エディタで.envを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=.env)

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=main.js)

### view-home.ejs

エディタでview-home.ejsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=view-home.ejs)

### view-input.ejs

エディタでview-input.ejsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=view-input.ejs)

### view-finish.ejs

エディタでview-finish.ejsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=view-finish.ejs)

### model.js

エディタでmodel.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=model.js)

### fixture.js

エディタでfixture.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=fixture.js)

### static-js-input.js

エディタでstatic-js-input.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=static-js-input.js)

### validate.js

エディタでvalidate.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=validate.js)

### api-initialize.js

エディタでapi-initialize.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=api-initialize.js)

### api-validate.js

エディタでapi-validate.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/32df3db218205f394eb072055df09abf?file=api-validate.js)

### api-submit.js

エディタでapi-submit.jsを開いて下記の内容を入力します。

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



## おわりに

データベースのレコードに基づいて動的にフォームを生成するGoogle Formsの回答ページのようなサブシステムを実装することができました。ただし、実装されたシステムはチュートリアルのために簡略化されています。さらにGoogle Formsに近づけるためには下記のような機能を設ける必要があります。

- 画像や動画の表示
- ファイルのアップロード
- 受付メールの送信
- テーマの設定

機能の追加に加え、下記のような点に取り組む必要があります。

- ユーザー体験の向上
- セキュリティの向上
- 運用性の向上
- アクセシビリティの向上

ユーザー体験の向上については一例としてバリデーションのエラーメッセージのリアルタイム表示などが挙げられます。そのためにはフロントエンドのJavaScriptでさまざまな処理を行う必要があり、その際にはwebpackなどのモジュールバンドラーが役に立つかも知れません。

セキュリティの向上については一例としてContent Security Policyの設定が挙げられます。Expressでは [Helmet](https://www.npmjs.com/package/helmet) というミドルウェアを使うことでセキュリティに関するHTTPヘッダを自動的に追加することができます。

運用性の向上については一例としてログ収集が挙げられます。Node.jsのログ収集ライブラリとしては [winston](https://www.npmjs.com/package/winston) が有名です。

アクセシビリティの向上については一例としてaria属性の設定が挙げられます。aria属性のを含む、アクセシビリティに関する情報についてはW3Cの WAI-ARIA Overview のページがとても有益です。

ユーザーがフォームを管理するためのサブシステムを作る方法については「Hana Forms Admin」の記事をお読みください。ご意見ご感想がありましたらお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！



## 関連記事

- [How to insert test data with Sequelize](https://gist.github.com/tatsuyasusukida/b3fea25a4619ae25034ddd3f35e5a450)
- [How to make foreign keys non-null with Sequelize](https://gist.github.com/tatsuyasusukida/547fb468b9bf352ddf9046e2f88a4d5c)
- [How to validate user input in Node.js](https://gist.github.com/tatsuyasusukida/fa28e2b0a8bb810b179556a42b946b97)
- [How to collect logs using winston in Node.js](https://gist.github.com/tatsuyasusukida/92b0f5dd8523ebdf19edb4a977a8030a)
