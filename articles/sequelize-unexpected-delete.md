---
title: "Sequelizeで子が親を削除するおもしろ動作を発見した"
emoji: "😳"
type: "tech"
topics: ["nodejs", "sequelize"]
published: true
---

## この記事について

この記事ではSequelizeを使っていて子モデルのレコードを削除した時に親モデルのレコードが削除されるかなり興味深い動作を発見したので自分用の備忘も兼ねて再現手順などについて説明します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/24f591f2b5f5305ce66fe628c53e8041#file-main-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/24f591f2b5f5305ce66fe628c53e8041)



## おおまかな流れ

再現手順のおおまかな流れを下記に示します。

- データベースの準備
- コーディングの準備
- コーディング
- 動作確認



## データベースの準備

下記のSQL文を発行してデータベースの準備をします。

@[gist](https://gist.github.com/tatsuyasusukida/24f591f2b5f5305ce66fe628c53e8041?file=db.sql)

ターミナルからSQL文を発行するには下記のコマンドを実行します。なお、パスワードを設定していない場合は-pオプションは不要です。

```shell
mysql -u root -p
```



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir sequelize-unexpected-delete
cd sequelize-unexpected-delete
npm init -y
npm install dotenv mysql2 sequelize
```



## コーディング

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/24f591f2b5f5305ce66fe628c53e8041?file=main.js)

### .env

エディタで.envを開いて下記の環境変数を入力します。

- DB_URL: データベース接続文字列

例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/24f591f2b5f5305ce66fe628c53e8041?file=.env)



## 動作確認

ターミナルで下記のコマンドを実行します。

```shell
node -r dotenv/config main.js
```

実行結果を下記に示します。なお、CREATE TABLE文、INSERT文、SELECT文、DELETE文だけを抜粋しています。

```
### await sequelize.sync({force: true})
CREATE TABLE IF NOT EXISTS `parents` (`id` INTEGER NOT NULL auto_increment , `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, PRIMARY KEY (`id`)) ENGINE=InnoDB;
CREATE TABLE IF NOT EXISTS `children` (`id` INTEGER NOT NULL auto_increment , `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, `parentId` INTEGER, PRIMARY KEY (`id`), FOREIGN KEY (`parentId`) REFERENCES `parents` (`id`) ON DELETE CASCADE ON UPDATE CASCADE) ENGINE=InnoDB;
CREATE TABLE IF NOT EXISTS `anotherChildren` (`id` INTEGER NOT NULL auto_increment , `createdAt` DATETIME NOT NULL, `updatedAt` DATETIME NOT NULL, `parentId` INTEGER, PRIMARY KEY (`id`), FOREIGN KEY (`parentId`) REFERENCES `parents` (`id`) ON DELETE CASCADE ON UPDATE CASCADE) ENGINE=InnoDB;

### const parent = await model.parent.create({})
INSERT INTO `parents` (`id`,`createdAt`,`updatedAt`) VALUES (DEFAULT,?,?); "2022-06-14 20:13:55", "2022-06-14 20:13:55"

### const child  = await model.child.create({parentId: parent.id})
INSERT INTO `children` (`id`,`createdAt`,`updatedAt`,`parentId`) VALUES (DEFAULT,?,?,?); "2022-06-14 20:13:55", "2022-06-14 20:13:55", 1

### const anotherChild  = await model.anotherChild.create({parentId: parent.id})
INSERT INTO `anotherChildren` (`id`,`createdAt`,`updatedAt`,`parentId`) VALUES (DEFAULT,?,?,?); "2022-06-14 20:13:55", "2022-06-14 20:13:55", 1

### await child.destroy()
SELECT `id`, `createdAt`, `updatedAt` FROM `parents` AS `parent` WHERE `parent`.`id` = 1;
DELETE FROM `parents` WHERE `id` = 1
DELETE FROM `children` WHERE `id` = 1
```

実行結果の最後の3行を見るとわかるように子モデルのレコードを削除するのに先立って親モデルのレコードが検索・削除されています。



## 発動条件

この現象を再現するための条件は下記の3点であると考えています。

1. 複数の子モデルが同じ1つの親モデルに対してbelongsToアソシエーションを設定している
2. belongsToアソシエーションを設定する時に同じオプションのオブジェクトを使用している
3. belongsToアソシエーションのオプションに `onDelete: 'cascade'` が含まれている



## 対策

belongsToアソシエーションを設定する時に同じオプションのオブジェクトを使用しないように変更することで、子モデルのレコードを削除した時に親モデルのレコードが削除されないようにすることができます。変更例を下記に示します。

```js
// before
const options = {onDelete: 'cascade'}
model.child.belongsTo(model.parent, options)
model.anotherChild.belongsTo(model.parent, options)

// after
const options = () => {onDelete: 'cascade'}
model.child.belongsTo(model.parent, options())
model.anotherChild.belongsTo(model.parent, options())
```

上記の変更後の実行結果を下記に示します。なお、INSERT文、DELETE文だけを抜粋しています。

```
### const parent = await model.parent.create({})
INSERT INTO `parents` (`id`,`createdAt`,`updatedAt`) VALUES (DEFAULT,?,?); "2022-06-14 20:13:55", "2022-06-14 20:13:55"

### const child  = await model.child.create({parentId: parent.id})
INSERT INTO `children` (`id`,`createdAt`,`updatedAt`,`parentId`) VALUES (DEFAULT,?,?,?); "2022-06-14 20:13:55", "2022-06-14 20:13:55", 1

### const anotherChild  = await model.anotherChild.create({parentId: parent.id})
INSERT INTO `anotherChildren` (`id`,`createdAt`,`updatedAt`,`parentId`) VALUES (DEFAULT,?,?,?); "2022-06-14 20:37:24", "2022-06-14 20:37:24", 1

### await child.destroy()
DELETE FROM `children` WHERE `id` = 1
```

子モデルのレコードを削除しても親モデルのレコードが削除されないようになりました。



## 検証環境

検証環境を下記に示します。

- 検証日: 2022年6月15日 水曜日
- Sequelizeのバージョン: 6.20.1
- Node.jsのバージョン: 16.13.0
- MySQLのバージョン: 8.0.27



## おわりに

この動作の発動条件や対策を調べるために少なくとも2時間を溶かしましたが、滅多にない貴重な経験ができて良かったとポジティブにとらえています。対策がわかったところで満足してしまったので原因までは調べていませんが、もし詳しい方がいらっしゃいましたらご指導のコメントをいただければ幸いです。この記事が同じ現象に遭遇して困っている方のお役に立てば嬉しいです。最後までお読みいただきありがとうございました。
