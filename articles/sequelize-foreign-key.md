---
title: "Node.jsのSequelizeで非NULLの外部キーを設ける方法"
emoji: "🍂"
type: "tech"
topics: ["nodejs"]
published: true
---

## この記事について

この記事ではNode.jsの [Sequelize](https://sequelize.org/) でbelongsToメソッドなどを使ってアソシエーションを設定した時に設けられる外部キーを非NULLにする方法について紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/547fb468b9bf352ddf9046e2f88a4d5c#file-main-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/547fb468b9bf352ddf9046e2f88a4d5c)
- [動画版](https://www.youtube.com/watch?v=NU5K7UFS4Wg)

https://www.youtube.com/watch?v=NU5K7UFS4Wg



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
mkdir sequelize-foreign-key
cd sequelize-foreign-key
npm init -y
npm install --save dotenv mysql2 sequelize
touch .env main.js
```



## コーディング

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/547fb468b9bf352ddf9046e2f88a4d5c?file=main.js)

ポイントを下記に示します。

1. belongsToメソッドのオプション（第2引数）のforeignKeyに `{allowNull: false}` を設定します。

### .env

エディタで.envを開いて下記を入力します。

- DB_URL: データベースの接続文字列（例：mysql://tech_blog:password@localhost:3306/tech_blog）

@[gist](https://gist.github.com/tatsuyasusukida/547fb468b9bf352ddf9046e2f88a4d5c?file=.env.example)



## 動作確認

ターミナルで下記のコマンドを実行してテーブルを作成します。

```shell
node -r dotenv/config main.js
```

実行結果に下記2点が含まれていることを確認します。

- `userId` INTEGER NOT NULL
- `articleId` INTEGER NOT NULL

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
```



## おわりに

以前はSequelizeの公式ドキュメントから外部キーを非NULLにする方法を探せなくて苦労した記憶があるのですが、今は [Associcationsのページ](https://sequelize.org/docs/v6/core-concepts/assocs/#mandatory-versus-optional-associations) に普通に掲載されているのですね。そうなるとこの記事の存在意義がなくなってしまう訳ですがせっかく書いたので枯れ木も山の賑わいということで投稿しようと思います。最後までお読みいただきありがとうございました！
