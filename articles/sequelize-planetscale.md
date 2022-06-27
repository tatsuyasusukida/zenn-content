---
title: "SequelizeからPlanetScaleにアクセスする方法"
emoji: "🌏"
type: "tech"
topics: ["nodejs", "sequelize", "planetscale"]
published: true
---

## この記事について

この記事ではNode.jsの[Sequelize](https://sequelize.org/)から[PlanetScale](https://planetscale.com/)のMySQLデータベースにアクセスする方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/f12e97732da431b090eb27935b420eb3#file-after-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/f12e97732da431b090eb27935b420eb3)


## Beforeのソースコード

PlanetScaleへのアクセスに対応する前のソースコードを下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/f12e97732da431b090eb27935b420eb3?file=before.js)



## Afterのソースコード

PlanetScaleへのアクセスに対応した後のソースコードを下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/f12e97732da431b090eb27935b420eb3?file=after.js)

ポイントを下記に示します。

1. Sequelizeのインスタンスを作成する時のdialectOptionsに `ssl: {rejectUnauthorized: true}` を指定してSSL接続を有効にします。
2. 【任意】テーブル名にプレフィックスを追加します。
3. 【任意】アソシエーションを作成する時のオプションにasを指定してプレフィックスなしでアクセスできるようにします。
4. アソシエーションを作成する時のオプションに `constraints: false` を指定して外部キー制約が作成されないようにします。
5. 【任意】クエリを発行する時はテーブル名にプレフィックスを追加します。



## 動作確認

### Beforeの実行結果

Beforeのソースコードを実行して発行されるSQL文を下記に示します。なお、CREATE文とINSERT文のみを抜粋しています。

```sql
CREATE TABLE IF NOT EXISTS `users` (
  `id` INTEGER NOT NULL auto_increment ,
  `createdAt` DATETIME NOT NULL,
  `updatedAt` DATETIME NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS `articles` (
  `id` INTEGER NOT NULL auto_increment ,
  `createdAt` DATETIME NOT NULL,
  `updatedAt` DATETIME NOT NULL,
  `userId` INTEGER NOT NULL,
  PRIMARY KEY (`id`),
  FOREIGN KEY (`userId`) REFERENCES `users` (`id`)
    ON DELETE NO ACTION
    ON UPDATE CASCADE
) ENGINE=InnoDB;

INSERT INTO `users` VALUES (NULL, NOW(), NOW())
```

実行結果を出力するためのコマンド例は下記の通りです。

```shell
DB_URL=mysql://ps_user:ps_pass@localhost:3306/ps_db \
  node before.js
```



### Afterの実行結果

Afterのソースコードを実行して発行されるSQL文を下記に示します。なお、CREATE文とINSERT文のみを抜粋しています。

```sql
CREATE TABLE IF NOT EXISTS `psUsers` (
  `id` INTEGER NOT NULL auto_increment ,
  `createdAt` DATETIME NOT NULL,
  `updatedAt` DATETIME NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS `psArticles` (
  `id` INTEGER NOT NULL auto_increment ,
  `createdAt` DATETIME NOT NULL,
  `updatedAt` DATETIME NOT NULL,
  `userId` INTEGER NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB;

INSERT INTO `psUsers` VALUES (NULL, NOW(), NOW());
```

実行結果を出力するためのコマンド例は下記の通りです。

```shell
DB_URL=mysql://xxxxxxxxxxxx:pscale_pw_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx@xxxxxxxxxxxx.ap-northeast-2.psdb.cloud/xxxx \
  node after.js
```

Afterの実行結果から下記の2点を読み取ることができます。

- テーブル名にpsのプレフィックスが追加されている点
- FOREIGN KEY制約が作成されていない点



## おわりに

PlanetScaleではSSLが必須なのでsequelizeのインスタンスを作成する時のdialectOptionsに `ssl: {rejectUnauthorized: true}` を指定してSSLを有効にする必要があります。また、PlanetScaleでは外部キー制約を設けることができないため、アソシエーションを作成する時のオプションに `constraints: false` を指定して外部キー制約が作成されないようにする必要があります。なお、外部キー制約を設けることができない点については [tak_iwamoto](https://zenn.dev/tak_iwamoto) さんが下記の記事で詳しく解説されています。

[PlanetScaleとは何か、なぜ外部キー制約をサポートしていないのか](https://zenn.dev/tak_iwamoto/articles/b27151d22d9e6a)

なお、テーブル名にプレフィックスを追加する点についてはPlanetScaleのFreeプランではデータベースを1つしか作成できないのでテーブル名が重複しないようにするための工夫です。アプリごとに複数のデータベースを作成できるのであればこのような工夫は不要です。

[PlanetScaleのPricing](https://planetscale.com/pricing)では一定のリソース消費量までは無料で使えるHobbyプランを提供しており、[Vercel](https://vercel.com/)などと組み合わせるとRDBを必要とするアプリであっても無料で運用することができるのでとても魅力的です。ただし、[VercelのPricing](https://vercel.com/pricing) によるとのHobbyプランは non-commercial & hobby sites の用途に限定されるので留意する必要があります。
