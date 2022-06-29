---
title: "SequelizeでCJSからESMへ移行する方法"
emoji: "🚀"
type: "tech"
topics: ["nodejs", "sequelize"]
published: true
---

## この記事について

この記事ではNode.jsの[Sequelize](https://sequelize.org/)でCommonJS(CJS)からES Modules(ESM)へ移行する方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/sequelize-cjs-to-esm)
- [English version / 英語版](https://github.com/tatsuyasusukida/sequelize-cjs-to-esm)



## CJSのソースコード

### index.js

CJSのmodels/index.js（`sequelize init` コマンドを実行すると生成されるmodels/index.js）の内容を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/4ab46713fd589886bc26f25e0a7a6504?file=index.js)

### モデル定義

モデル定義の一例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/4ab46713fd589886bc26f25e0a7a6504?file=article.js)

### 使用例

使用例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/4ab46713fd589886bc26f25e0a7a6504?file=main-cjs.js)



## ESMのソースコード

### index.mjs

ESMのmodels/index.jsの内容を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/1e16db81ca8b38e8d77ccd88cb121f31?file=index.mjs)

ポイントを下記に示します。

1. モデル定義の関数を静的にインポートします。CJSの場合はrequireがasyncではないので動的にインポートできますが、ESMの場合はimportがasyncなので静的にインポートする必要があります。
2. 環境変数を使ってSequelizeのインスタンスを作成します。この点はESMへの移行に不要ですがコンテナ化する時に便利なので変更しています。
3. モデル定義の関数にを呼び出してモデルのインスタンスを作成します。
4. モデルのassociate関数を呼び出してアソシエーションを作成します。
5. modelsをエクスポートします。

### モデル定義

モデル定義の一例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/1e16db81ca8b38e8d77ccd88cb121f31?file=article.mjs)

変更点は下記の通りです。

- 変更前: `module.exports = `
- 変更後: `export default`

### 使用例

使用例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/1e16db81ca8b38e8d77ccd88cb121f31?file=main-esm.mjs)

変更点は下記の通りです。

- 変更前: `const models = require('./models')`
- 変更後: `import models from './models/index.mjs'`



## 動作確認

CJSとESMのソースコードを実行して発行されるSQL文を下記に示します。当然ですが内容に違いはありません。なお、CREATE文とINSERT文のみを抜粋しています。

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
```

実行結果を出力するためのコマンド例は下記の通りです。

```shell
# CJS
node main-cjs.js

# ESM
DB_URL=mysql://tech_blog_user:tech_blog_pass@localhost:3306/tech_blog_db \
  node main-esm.js
```



## おわりに

ESMのmodels/index.mjsではモデル定義を静的にインポートしているので、新しいモデル定義のファイルを追加する度にindex.mjsを更新する必要があり、この点がCJSと比べて不便です。良い方法をご存知の方がいましたらご指導のコメントをいただければ幸いです。最後までお読みいただきありがとうございました！
