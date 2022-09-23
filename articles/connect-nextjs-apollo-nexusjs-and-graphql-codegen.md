---
title: "Next.jsとApolloとNexus.jsとGraphQL Code Generatorをつなげる"
emoji: "🏭"
type: "tech"
topics: ["nextjs", "apollo", "nexus", "graphql", "graphqlcodegen"]
published: true
---

## この記事について

この記事ではNext.jsのAPI Routeに[apollo-server-micro@3](https://www.npmjs.com/package/apollo-server-micro)を使ってApollo Serverを立て、さらに[Nexus.js](https://nexusjs.org/)を使ってスキーマとリゾルバーを設定し、さらにさらに[GraphQL Code Generator](https://www.the-guild.dev/graphql/codegen)を使ってフロントエンド用のTypeScriptを自動生成する方法について紹介します。なお、フロントエンド側の動作を確認するにあたり[urql](https://formidable.com/open-source/urql/)を使用しています。この記事の関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/next-apollo-nexus-codegen)

関連記事を下記に示します。

- [Next.js + Apollo](https://zenn.dev/tatsuyasusukida/articles/applo-server-3-in-api-route-of-nextjs)
- [Next.js + Apollo + Nexus.js](https://zenn.dev/tatsuyasusukida/articles/connect-nextjs-apollo-and-nexusjs)



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```sh
npx create-next-app --typescript next-apollo-nexus-codegen
cd next-apollo-nexus-codegen
npm install --save apollo-server-micro graphql nexus urql
npm install --save-dev concurrently \
  @graphql-codegen/cli \
  @graphql-codegen/typed-document-node \
  @graphql-codegen/typescript \
  @graphql-codegen/typescript-operations
mkdir api
touch api/schema.ts
mkdir api/graphql
touch api/graphql/index.ts
touch api/graphql/post.ts
touch pages/api/graphql.ts
touch pages/client.tsx
touch codegen.yml
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### pages/api/graphql.ts

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=graphql.ts)

### api/graphql/post.ts

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=post.ts)

### api/graphql/index.ts

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=index.ts)

### api/schema.ts

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=schema.ts)

### pages/client.tsx

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=client.tsx)

### pages/_app.tsx

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=_app.tsx)

### codegen.yml

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=codegen.yml)

codegen.ymlは下記のコマンドを実行して対話的に作成することもできます。

```sh
npx graphql-code-generator init
```

回答内容を下記に示します。

```
? What type of application are you building?
Application built with React

? Where is your schema?: (path or url)
generated/schema.graphql

? Where are your operations and fragments?:
pages/**/*.tsx

? Pick plugins:
TypeScript (required by other typescript plugins),
TypeScript Operations (operations and fragments)

? Where to write the output:
generated/codegen.ts

? Do you want to generate an introspection file?
No

? How to name the config file?
codegen.yml

? What script in package.json should run the codegen?
generate
```

`typed-document-node`についてはgenerates > generated/graphql.ts > pluginsに手動で追加する必要があります。



## 動作確認

ターミナルで下記のコマンドを実行してサーバーを起動すると共に、GraphQL Codge Generatorをwatchモードで起動します。

```sh
npx concurrently 'next dev' 'graphql-codegen --config codegen.yml --watch'
```

下記の3つのファイルが出力されていることを確認します。

- generated/nexus-typegen.ts
- generated/schema.graphql
- generated/codegen.ts

generated/schema.graphqlの内容が下記と同じであることを確認します。

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=schema.graphql)

generated/codegen.tsの内容が下記と同じであることを確認します。

@[gist](https://gist.github.com/tatsuyasusukida/505c1ba248be6d57f7b5d8a6a19a271b?file=codegen.ts)

generated/nexus-typegen.tsについては内容が多いので確認を割愛します。

http://localhost:3000/client にアクセスしてApolloのExplorerページにアクセスして下記の内容が表示されることを確認します。

```graphql
[
  {
    "id": 1,
    "title": "title",
    "body": "body",
    "published": false,
    "__typename": "Post"
  }
]
```



## おわりに

Nexus.jsとGraphQL Code Generatorを併用することでバックエンド／フロントエンド両方のTypeScript型定義が自動生成されるのでとても便利です。なお、GraphQL Code Generator公式ドキュメントの[Guide: GraphQL Yoga / Apollo Server](https://www.the-guild.dev/graphql/codegen/docs/guides/graphql-server-apollo-yoga)ページによるとGraphQL Code Generator単体でもバックエンド／フロントエンド両方のTypeScript型定義を自動生成できます。「それならNexus.jsは必要なくない？」という感じもしますがNexus.jsを使うとスキーマの近くでリゾルバーを定義できる点や使用言語をTypeScriptに統一できる点にメリットを感じるのでNexus.jsを使用しています。

この記事ではtsxファイルにGraphQLクエリを書いていますが公式ドキュメントによるとこの方法は推奨されず、別途graphqlファイルを用意するべきだとの記載があります。

[Guide: React and GraphQL | GraphQL Code Generator](https://www.the-guild.dev/graphql/codegen/docs/guides/react#optimal-configuration-for-apollo-and-urql)

> To have @graphql-codegen/typed-document-node working and avoid code duplication, we highly recommend moving all gql document declarations outside of .tsx/.ts files. For this, create a colocated .graphql file for each GraphQL document, as follows:

個人的にはtsxファイル内にGraphQLクエリを書いた方がtsxファイルだけで完結するので好きですが、あまり推奨されていないということなので実際の開発では公式ドキュメントに従って別途graphqlファイルを用意しようと思います。
