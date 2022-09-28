---
title: "Next.js 12.2からはEmotionが簡単に使える"
emoji: "💎"
type: "tech"
topics: ["nextjs", "emotion", "typescript"]
published: true
---

## この記事について

Next.js公式ブログの「Next.js 12.2」というタイトルの記事によると、2022年6月22日にリリースされたNext.js 12.2からはEmotionが標準でサポートされるようになりました。

https://nextjs.org/blog/next-12-2

これにより、@emotion/babel-pluginのインストールが不要になったため、従来よりも簡単にEmotionを利用できるようになりました。この記事ではNext.jsのバージョン12.2以降で@emotion/babel-pluginをインストールすることなくEmotionを使う方法について紹介します。



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```sh
npx create-next-app --typescript nextjs-emotion
cd nextjs-emotion
npm install --save @emotion/react @emotion/styled
touch pages/react.tsx
touch pages/styled.tsx
npm run dev
```



## next.config.jsの設定

エディタでnext.config.jsを開いて下記の設定を追加します。

```js:next.config.js
compiler: {
  emotion: true
}
```

これにより、@emotion/reactが利用できるようになります。なお、@emotion/styledを使う場合はこの設定がなくても大丈夫な様子です。詳しい設定内容については下記のNext.js公式ドキュメントに説明があります。

https://nextjs.org/docs/advanced-features/compiler#emotion



## tsconfig.jsonの設定

```js:tsconfig.json
"jsxImportSource": "@emotion/react"
```

これにより、tsxファイルで@emotion/reactのcssプロパティを使ったときにエディタでエラーが表示されなくなります。なお、@emotion/styledを使う場合にはこの設定がなくても大丈夫な様子です。



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### pages/react.tsx

@[gist](https://gist.github.com/tatsuyasusukida/3e867f8df4a69f867633c5ebf21e350f?file=react.tsx)

### pages/styled.tsx

@[gist](https://gist.github.com/tatsuyasusukida/3e867f8df4a69f867633c5ebf21e350f?file=styled.tsx)



## 動作確認

ターミナルで下記のコマンドを実行してサーバーを起動します。

```sh
npm run dev
```

ブラウザで http://localhost:3000/react にアクセスしてdivにスタイルが適用されていることを確認します。

続いてブラウザで http://localhost:3000/styled にアクセスしてbuttonにスタイルが適用されていることを確認します。

### 参考画像

#### @emotion/reactを使ったページ

![](/images/articles/easy-to-use-emotion-from-next.js-12.2/react.png)

#### @emotion/styledを使ったページ

![](/images/articles/easy-to-use-emotion-from-next.js-12.2/styled.png)



## おわりに

iwakin999さんによる下記の記事によるとNext.jsのバージョン12.2以前は@emotion/babel-pluginのインストールや下記の内容の.babelrcファイルの作成など少し手間が多かったようです。

```json:.babelrc
{
  "presets": [
    [
      "next/babel",
      {
        "preset-react": {
          "runtime": "automatic",
          "importSource": "@emotion/react"
        }
      }
    ]
  ],
  "plugins": ["@emotion/babel-plugin"]
}
```

https://zenn.dev/iwakin999/articles/7a5e11e62ba668

なお、Next.js公式ドキュメントやブログに記載がある通り@emotion/babel-pluginのimportMapの機能は現時点ではサポートされていないため、importMapの機能を利用したい場合は引き続きiwakin999さんの記事で紹介されている.babelrcファイルを作成する方法を使う必要があリます。

また、iwakin999さんの記事でLinkコンポーネントを使ったときにaタグのhref属性が自動生成されない問題について言及されていますが、この記事で紹介した方法の場合も同様の問題が発生します。問題を回避するには下記のようにLinkコンポーネントにpassHrefを設定する必要があります。

```js
<Link href="/styled" passHref>
  <a css={css`color: red`}>@emotion/styled</a>
</Link>
```
