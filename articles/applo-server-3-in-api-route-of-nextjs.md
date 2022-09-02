---
title: "Next.jsのAPI RouteにApplo Server 3を立てる"
emoji: "💎"
type: "tech"
topics: ["graphql", "nextjs", "react", "apollo"]
published: true
---

## この記事について

この記事ではNext.jsのAPI Routeに[apollo-server-micro@3](https://www.npmjs.com/package/apollo-server-micro)を使ってApollo Serverを立てる方法を紹介します。関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/next-apollo)



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```sh
npx create-next-app --typescript next-apollo
cd next-apollo
npm install --save apollo-server-micro@3 graphql @apollo/client
touch pages/api/graphql.ts
touch pages/client.ts
npm run dev
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### pages/api/graphql.ts

@[gist](https://gist.github.com/tatsuyasusukida/6d0c65b76cffbaedf55619e4c2da6a31?file=graphql.ts)

### pages/_app.tsx

@[gist](https://gist.github.com/tatsuyasusukida/6d0c65b76cffbaedf55619e4c2da6a31?file=_app.tsx)

### pages/client.tsx

@[gist](https://gist.github.com/tatsuyasusukida/6d0c65b76cffbaedf55619e4c2da6a31?file=client.tsx)



## 動作確認

ブラウザで http://localhost:3000/api/graphql にアクセスしてApollo Serverページが表示されることを確認します。

Query your serverボタンをクリックしてExplorerページへ移動してから下記のクエリを実行して成功することを確認します。

```graphql
query SayHello {
  sayHello
}
```

ブラウザで http://localhost:3000/client にアクセスして下記の内容が表示されることを確認します。

{"sayHello":"Hello World!"}

### 参考画像

#### Apollo Serverページ

![](/images/articles/applo-server-3-in-api-route-of-nextjs/img-check-01.png)

#### Explorerページ

![](/images/articles/applo-server-3-in-api-route-of-nextjs/img-check-02.png)

#### Clientページ

![](/images/articles/applo-server-3-in-api-route-of-nextjs/img-check-03.png)



## おわりに

Next.jsのAPI RouteにApollo Serverを立てる方法については[ryo_kawamata](https://zenn.dev/ryo_kawamata)さんが[Next.js の API Routes に Apollo Server を立てる](https://zenn.dev/ryo_kawamata/articles/how-to-create-apollo-server-on-nextjs-api-routes)の記事に簡潔にわかりやすくまとめてくれています。

apollo-server-microのバージョン3では`apolloServer.createHandler`を呼び出す前に`apolloServer.start`を呼び出す必要があるので、少し長くなりますが下記のように書き換える必要があります。

```ts:before.ts
const apolloServer = new ApolloServer({ typeDefs, resolvers })
export default apolloServer.createHandler({ path: "/api/graphql" })
```

```ts:after.ts
const apolloServer = new ApolloServer({ typeDefs, resolvers })
const handler = apolloServer.start()
  .then(() => apolloServer.createHandler({ path: '/api/graphql' }))

export default async function (req: NextApiRequest, res: NextApiResponse) {
  await (await handler)(req, res)
}
```

### CORSについて

ApolloのExplorerページからApollo ServerにアクセスできるようにするにはCORS設定を行う必要があります。設定手順については[micro-cors](https://www.npmjs.com/package/micro-cors)を使った方法が[apollo-server-micro](https://github.com/apollographql/apollo-server/tree/main/packages/apollo-server-micro#cors-example)のページで紹介されています。

```js
const apolloServer = new ApolloServer({ typeDefs, resolvers });
module.exports = apolloServer.start().then(() => {
  const handler = apolloServer.createHandler();
  return cors((req, res) => req.method === 'OPTIONS' ? send(res, 200, 'ok') : handler(req, res))
});
```

公式の方法をそのまま利用しても良かったのですがmicro-corsを使うとTypeScriptのエラーメッセージが表示されのに加え、ソースコードがかえって複雑になる印象を受けたので下記のように直接CORSのヘッダーを指定しています。

```ts
export default async function (req: NextApiRequest, res: NextApiResponse) {
  if (process.env.NODE_ENV === 'development') {
    res.setHeader('Access-Control-Allow-Origin', '*')
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type')

    if (req.method === 'OPTIONS') {
      res.status(200).end()
      return
    }
  }

  await (await handler)(req, res)
}
```
