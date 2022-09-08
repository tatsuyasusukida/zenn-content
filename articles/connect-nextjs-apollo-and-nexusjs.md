---
title: "Next.jsとApolloとNexus.jsをつなげる"
emoji: "📮"
type: "tech"
topics: ["nextjs", "apollo", "nexus", "graphql", "typescript"]
published: true
---

## この記事について

この記事ではNext.jsのAPI Routeに[apollo-server-micro@3](https://www.npmjs.com/package/apollo-server-micro)を使ってApollo Serverを立て、さらに[Nexus.js](https://nexusjs.org/)を使ってスキーマとリゾルバーを設定する方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/nextjs-apollo-nexus)

関連記事を下記に示します。

- [Next.js + Apollo](https://zenn.dev/tatsuyasusukida/articles/applo-server-3-in-api-route-of-nextjs)


## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```sh
npx create-next-app --typescript next-apollo-nexus
cd next-apollo-nexus
npm install --save apollo-server-micro graphql nexus
mkdir api
touch api/schema.ts
mkdir api/graphql
touch api/graphql/index.ts
touch api/graphql/post.ts
touch pages/api/graphql.ts
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### pages/api/graphql.ts

@[gist](https://gist.github.com/tatsuyasusukida/0daa727b457e1e6d83814293905e1abc?file=graphql.ts)

### api/graphql/post.ts

@[gist](https://gist.github.com/tatsuyasusukida/0daa727b457e1e6d83814293905e1abc?file=post.ts)

### api/graphql/index.ts

@[gist](https://gist.github.com/tatsuyasusukida/0daa727b457e1e6d83814293905e1abc?file=index.ts)

### api/schema.ts

@[gist](https://gist.github.com/tatsuyasusukida/0daa727b457e1e6d83814293905e1abc?file=schema.ts)



## 動作確認

ターミナルで下記のコマンドを実行してサーバーを起動します。

```sh
npm run dev
```

下記の2つのファイルが出力されていることを確認します。

- generated/nexus-typegen.ts
- generated/schema.graphql

generated/schema.graphqlの内容が下記と同じであることを確認します（generated/nexus-typegen.tsについては内容が多いので割愛します）。

@[gist](https://gist.github.com/tatsuyasusukida/0daa727b457e1e6d83814293905e1abc?file=schema.graphql)

http://localhost:3000/api/graphql にアクセスしてApolloのExplorerページにアクセスして下記のクエリを実行します。

```graphql
query Query {
  drafts {
    id
    title
    body
    published
  }
}
```

クエリの実行結果が下記と同じであることを確認します。

```json
{
  "data": {
    "drafts": [
      {
        "id": 1,
        "title": "title",
        "body": "body",
        "published": false
      }
    ]
  }
}
```



## おわりに

Nexus.jsを使うことでGraphQLスキーマからバックエンド用のTypeScript型定義が自動生成されるのでとても便利です。Nexus.jsが自動生成する型定義についてはフロントエンドで利用できないこともないですが[GraphQL Code Generator](https://www.the-guild.dev/graphql/codegen)を併用した方が良いと考えています。
