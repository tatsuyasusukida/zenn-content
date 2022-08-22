---
title: "モダンフロントエンドへの移行が辛い"
emoji: "😱"
type: "idea"
topics: ["frontend", "typescript", "nextjs", "react", "apollo"]
published: true
---

## この記事について

ここ最近まで勉強をサボって10年くらい前のWeb技術を使い続けてきたのですが、Zennでフロントエンドの先人たちが投稿している記事を読んで新しい技術に移行したいと思うようになりました。この記事では何から何へ移行しているのかをまとめたいと思います。



## JavaScriptからTypeScriptへ

[TypeScript](https://www.typescriptlang.org/)は5年くらい前に一度仕事で使ったのですが、すべてに型をつけなければならない強迫観念にかられるようになり、手間が煩わしくなってJavaScriptに戻した経緯があります。JavaScriptでも良いのですがTypeScriptの方がコード補完や警告メッセージがあるので安心してプログラミングができます。今はJavaScriptとほとんど同じように書いていますが、いずれは型を意識して書けるように精進したいと思います。



## Express+webpackからNext.jsへ

Webフレームワークには[Express](https://expressjs.com/)と[webpack](https://webpack.js.org/)を組み合わせて使っていました。理由としてはHTMLのコーディングに[Pug](https://pugjs.org/)を使いたいとか、ReactではなくてVue.jsを使いたいとか些細なこだわりでした。[Next.js](https://nextjs.org/)を使うようになり、これまで自分で設定を書いたり、スクリプトを書いたり、ミドルウェアを書いたりして頑張っていたことが必要なくなったのでとても楽です。



## Vue.jsからReactへ

JavaScriptの中にHTMLがある[JSX](https://reactjs.org/docs/introducing-jsx.html)を受け入れられず消去法で[Vue.js](https://vuejs.org/)を選びましたが、テストのしやすさなどを考えるとJSXの方にメリットがあると考えるようになりました。また、いちいちクラスを書かなければならないのも面倒そうだと思っていましたが[Hooks](https://reactjs.org/docs/hooks-intro.html)を使うと簡単に書けることを知って見方が変わりました。まだ[Redux](https://react-redux.js.org/)などの周辺技術を理解できていないので勉強を頑張ろうと思います。



## RESTからGraphQLへ

現状ではRESTでも特に不足を感じていませんがTypeScriptとの相性を考えるといずれは[GraphQL](https://graphql.org/)に移行することになるだろうと思い、少しずつ勉強を始めています。1回のリクエストで関連するデータを一気に取得できる点やクライアント側でクエリを変更できる点はRESTにはない魅力だと思います（もちろんRESTでも頑張れば同じことはできますが...）。ApolloのGraphQLチュートリアルの[Odyssey](https://www.apollographql.com/tutorials/)は素晴らしい教材だと思います。



## BootstrapからTailwind CSSへ

[Bootstrap](https://getbootstrap.com/)は管理システムなどのシンプルなUIを作るのには便利ですが、見栄えの良さを追求したい場合は[Tailwind CSS](https://tailwindcss.com/)の方がかゆい所に手が届きそうな感じがするので習得したいです。でもなかなかクラス名を覚えられません... [MUI](https://mui.com/)や[Chakura UI](https://chakra-ui.com/)も気になります。



## SequelizeからPrismaへ

RESTの場合と同様、現状では[Sequelize](https://sequelize.org/)でも特に不足は感じていませんがTypeScriptとの相性を考えるといずれは[Prisma](https://www.prisma.io/)に移行することになるだろうと思い、少しずつ勉強を始めています。`prisma generate`は若干黒魔術っぽい感じがしますが特に何も設定しなくてもエディタで型の情報が表示されるのはとても快適ですね。



## Sublime TextからVisual Studio Code

数ヶ月前に2万円くらい払って[Sublime Text](https://www.sublimetext.com/)のライセンスを更新したばかりなのですが、提供されるプラグイン（エクステンション）の豊富さなどを考慮すると早期に[Visual Studio Code](https://azure.microsoft.com/products/visual-studio-code/)に移行した方が得だと考えるようになりました。この記事もVisual Studio Codeを使って書いています。幸いなことにSublime TextとVisual Studio Codeでキーボードショートカットが共通しているものが多いのでそれほどストレスなく移行できています。今はキーボードショートカットのリファレンスを毎日のように見るようにして習得中です。

- [Visual Studio Code Keyboard Shortcuts for macOS](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-macos.pdf)



## おわりに

新しい技術へ移行することは学ぶことが多くてしんどいですが楽しいですね。「これも学んでおいた方が良いよ」などのアドバイスがありましたらご指導のコメントをいただければ幸いです。
