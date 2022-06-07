---
title: "Sequelizeでテストデータを登録する方法"
emoji: "🧐"
type: "tech"
topics: ["nodejs", "sequelize"]
published: false
---

## この記事について

この記事ではNode.jsのORMである [Sequelize](https://sequelize.org/) を使ってデータベースにテストデータを登録する方法について紹介します。関連リソースを下記に示します。

- [English version / 英語版](https://gist.github.com/tatsuyasusukida/b3fea25a4619ae25034ddd3f35e5a450)



## おおまかな手順

おおまか手順を下記に示します。

1. データベースの作成
2. コーディングの準備
3. コーディング
4. 動作確認



## データベースの作成

下記のSQL文を実行して動作確認用のデータベースを作成します。

```sql
create database tech_blog charset utf8;
create user tech_blog@localhost identified by 'password';
grant all privileges on tech_blog.* to tech_blog@localhost;
```

ターミナルからSQL文を実行するには下記のコマンドを実行します（パスワードを設定していない場合は-pオプションは不要です）。

```shell
mysql -u root -p
```



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir sequelize-test-data
cd sequelize-test-data
npm init -y
npm install --save dotenv mysql2 sequelize
touch .env main.js
```



## コーディング

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/b3fea25a4619ae25034ddd3f35e5a450?file=main.js)

ポイントを下記に示します。

1. モデルのcreateメソッドを呼び出してレコードを挿入します。戻り値を変数に代入しておくことで後からIDなどを取得することができます。
2. 外部キーを持つテーブルにレコードを挿入する場合、参照先のテーブルにレコードをあらかじめ挿入してIDを確定させます。

### .env

エディタで.envを開いて下記1点を入力します。

- DB_URL: データベースの接続文字列（例：mysql://tech_blog:password@localhost:3306/tech_blog）

例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/b3fea25a4619ae25034ddd3f35e5a450?file=.env.example)



## 動作確認

ターミナルで下記のコマンドを実行してテーブルを作成します。

```shell
node -r dotenv/config main.js
```

実行結果に下記2点が含まれていることを確認します。

- userテーブルへのINSERT文
- articleテーブルへのINSERT文
- userArticleテーブルへのINSERT文

実行結果を下記に示します。

```
Executing (default): DROP TABLE IF EXISTS `userArticle`;
Executing (default): DROP TABLE IF EXISTS `article`;
Executing (default): DROP TABLE IF EXISTS `user`;
Executing (default): DROP TABLE IF EXISTS `user`;
Executing (default): CREATE TABLE IF NOT EXISTS `user` (`id` INTEGER NOT NULL auto_increment , `name` VARCHAR(255) NOT NULL, `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, PRIMARY KEY (`id`)) ENGINE=InnoDB;
Executing (default): SHOW INDEX FROM `user`
Executing (default): DROP TABLE IF EXISTS `article`;
Executing (default): CREATE TABLE IF NOT EXISTS `article` (`id` INTEGER NOT NULL auto_increment , `title` VARCHAR(255) NOT NULL, `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, PRIMARY KEY (`id`)) ENGINE=InnoDB;
Executing (default): SHOW INDEX FROM `article`
Executing (default): DROP TABLE IF EXISTS `userArticle`;
Executing (default): CREATE TABLE IF NOT EXISTS `userArticle` (`id` INTEGER NOT NULL auto_increment , `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, `userId` INTEGER NOT NULL, `articleId` INTEGER NOT NULL, PRIMARY KEY (`id`), FOREIGN KEY (`userId`) REFERENCES `user` (`id`) ON DELETE CASCADE ON UPDATE CASCADE, FOREIGN KEY (`articleId`) REFERENCES `article` (`id`) ON DELETE CASCADE ON UPDATE CASCADE) ENGINE=InnoDB;
Executing (default): SHOW INDEX FROM `userArticle`
Executing (default): INSERT INTO `user` (`id`,`name`,`createdAt`,`updatedAt`) VALUES (DEFAULT,?,?,?); "NAME", "2022-05-05 00:13:05", "2022-05-05 00:13:05"
Executing (default): INSERT INTO `article` (`id`,`title`,`createdAt`,`updatedAt`) VALUES (DEFAULT,?,?,?); "TITLE", "2022-05-05 00:13:05", "2022-05-05 00:13:05"
Executing (default): INSERT INTO `userArticle` (`id`,`createdAt`,`updatedAt`,`userId`,`articleId`) VALUES (DEFAULT,?,?,?,?); "2022-05-05 00:13:05", "2022-05-05 00:13:05", 1, 1
```



## 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [便利！Node.jsにDBマイグレーション、Seed、モデルを用意する「Sequelize」](https://blog.capilano-fw.com/?p=5546)



## おわりに

Sequelizeは [マイグレーション](https://sequelize.org/docs/v6/other-topics/migrations/) の機能を備えており、テストデータを登録する正当な方法はマイグレーションの機能を使う方法ではないかと思います。この記事で紹介した我流の方法にメリットがあるとすればAPIテスト時にデータベースを初期化したい時にテストコードから呼び出すのが簡単である点などでしょうか（マイグレーションの機能を使う場合もqueryInterfaceなどを用意できればできるのかも知れませんが...）。

我流の方法にこだわっていると発展性に乏しいように思えるので何かの機会にSequelizeのマイグレーション機能をしっかり勉強し、早めに正当な方法に切り替えられるように努力したいと思います。最後までお読みいただきありがとうございました！
