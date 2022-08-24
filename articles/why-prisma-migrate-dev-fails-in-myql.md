---
title: "MySQLでprisma migrate devが失敗する"
emoji: "🦋"
type: "tech"
topics: ["javascript", "webpack"]
published: true
---



## この記事について

MySQLで[Prisma](https://www.prisma.io/)を使っていて`prisma migrate dev`を実行した時に"Prisma Migrate could not create the shadow database."とエラーメッセージが表示されてマイグレーションが失敗する現象に遭遇しました。この記事ではマイグレーション失敗の再現手順、原因、解決方法についてまとめます。



## 再現手順

ターミナルで下記のコードを実行してコーディングの準備をします。

```shell
mkdir prisma-migrate
cd prisma-migrate
npm init -y
npm install --save prisma
npx prisma init --datasource-provider mysql
touch db.sql
```

エディタで下記のファイルを開いて内容を入力します。

```prisma:prisma/schema.prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}

model Post {
  id    Int    @id @default(autoincrement())
  title String
}
```

```.env
DATABASE_URL="mysql://prisma_user:prisma_pass@localhost:3306/prisma_db"
```

```sql:db.sql
create database prisma_db charset utf8;
create user prisma_user@localhost identified by 'prisma_pass';
grant all privileges on prisma_db.* to prisma_user@localhost;
```

ターミナルで下記のコマンドを実行してデータベースの準備をします。なお、パスワードを設定していない場合は`-p`オプションは不要です。

```shell
mysql -u root -p < db.sql
```

ターミナルで下記のコマンドを実行してマイグレーションを実行します。

```shell
npx prisma migrate dev
```

マイグレーションが失敗し、下記のエラーメッセージが表示されることを確認します。

```
Error: P3014

Prisma Migrate could not create the shadow database. Please make sure the database user has permission to create databases. Read more about the shadow database (and workarounds) at https://pris.ly/d/migrate-shadow

Original error: Error code: P1010

User `prisma_user` was denied access on the database `prisma_db`
```



## 失敗の原因

`prisma migrate dev`コマンドを実行するとshadow databaseと呼ばれる一時的なデータベースがマイグレーションのために作成されます。この際、データベースに接続するユーザー（上記の再現手順ではprisma_user@localhost）にshadow databaseを作成する権限がない場合、マイグレーションが実行できないので失敗してエラーメッセージが表示されます。詳細についてはPrisma公式ドキュメントの[Shadow Database](https://www.prisma.io/docs/concepts/components/prisma-migrate/shadow-database)のページで説明されています。なお、このページへのリンクはエラーメッセージにも含まれています。



## 対処方法

Prisma公式ドキュメントのShadow Databaseページの[Shadow database user permissions](https://www.prisma.io/docs/concepts/components/prisma-migrate/shadow-database#shadow-database-user-permissions)セクションに記載がある通り、データベースに接続するユーザーにCREATE, ALTER, DROP, REFERENCES ON *.*の権限を与えます。これらの権限を与えるには下記のSQL文を実行します。

```sql
grant all CREATE, ALTER, DROP, REFERENCES ON *.* to prisma_user@localhost;
```

データベースを作成する時に下記のようにセットで設定するのもおすすめです。

```sql:db.sql
create database prisma_db charset utf8;
create user prisma_user@localhost identified by 'prisma_pass';
grant all privileges on prisma_db.* to prisma_user@localhost;
grant all create, alter, drop, references on *.* to prisma_user@localhost;
```

なお、CREATE, ALTER, DROP, REFERENCES ON *.*の権限は強力なので開発環境以外では設定しないように留意します。



## おわりに

長年愛用していた[Sequelize](https://sequelize.org/)からPrismaへ移行しています。公式ドキュメントのMySQL向けの[Getting Started](https://www.prisma.io/docs/getting-started/setup-prisma/start-from-scratch/relational-databases-typescript-mysql)チュートリアルで勉強していたところこのエラーに遭遇して早速つまづいてしまいましたが、幸いQiitaユーザーさんが下記の記事にまとめていてくれたので早期に解決することができました。先人に感謝！

[PrismaでMySQLを利用して開始するときにエラーが発生する場合](https://qiita.com/makoll/items/4df94068f570aebf39af)
