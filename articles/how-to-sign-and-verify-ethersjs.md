---
title: "ethers.jsを使ってMetaMaskで署名してNode.jsで検証する方法"
emoji: "✍️"
type: "tech"
topics: ["ethereum", "metamask", "web3", "ethersjs", "nodejs"]
published: true
---

## この記事について

この記事では[ethers.js](https://www.npmjs.com/package/ethers)を使ってフロントエンドのMetaMaskで署名してバックエンドのNode.jsで検証する方法について紹介します。この記事の関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/how-to-sign-and-verify-using-web3js)
- [web3.js版](https://zenn.dev/tatsuyasusukida/articles/how-to-sign-and-verify-using-web3js)

## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
npx create-next-app --typescript how-to-sign-and-verify-using-ethersjs
cd how-to-sign-and-verify-using-ethersjs
touch pages/sign.tsx pages/api/verify.ts
npm install --save ethers
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### pages/sign.tsx

@[gist](https://gist.github.com/tatsuyasusukida/af5e160fac7b88c151ebca6b653b9135?file=sign.tsx)

### pages/api/verify.ts

@[gist](https://gist.github.com/tatsuyasusukida/af5e160fac7b88c151ebca6b653b9135?file=verify.ts)



## 動作確認

ターミナルで下記のコマンドを実行してサーバーを起動します。

```shell
npm run dev
```

ブラウザで http://localhost:3000/sign にアクセスします。

Signボタンをクリックします。

MetaMask Notificationポップアップが表示されたらSignボタンをクリックします。

Signボタンの直後にVerified!とメッセージが表示されることを確認します。

### 参考画像

![](/images/articles/how-to-sign-and-verify-using-web3js/img-check-01.jpg)

![](/images/articles/how-to-sign-and-verify-using-web3js/img-check-02.jpg)

![](/images/articles/how-to-sign-and-verify-using-web3js/img-check-03.jpg)



## おわりに

下記の記事で紹介されているように署名と検証を利用してユーザー認証を行うことができます。

[DApps のユーザー認証に web3.eth.personal.sign を使おう！ - Tech Inside Drecom](https://tech.drecom.co.jp/dapps-use-web3-eth-personal-sign/)

初めはweb3.jsを使おうと思い、署名と検証の方法を下記の記事にまとめました。

[web3.jsを使ってMetaMaskで署名してNode.jsで検証する方法](https://zenn.dev/tatsuyasusukida/articles/how-to-sign-and-verify-using-web3js)

途中でethers.jsを使いたくなってこの記事を投稿した次第です。

### web3.jsとethers.jsの比較

web3.jsとethers.jsの署名の手順についてはそれぞれ下記の通りです。

```js:web3js-sign.js
const web3 = new Web3(window.ethereum)
const message = 'message'
const [address] = await web3.eth.personal.getAccounts()
const password = ''
const signature = await web3.eth.personal.sign(message, address, password)
```

```js:ethers-sign.js
const provider = new ethers.providers.Web3Provider(window.ethereum)
const message = 'message'
const signer = await provider.getSigner()
const signature = await signer.signMessage(message)
```

また、web3.jsとethers.jsの検証の手順についてはそれぞれ下記の通りです。

```js:web3js-verify.js
const web3 = new Web3()
const address = web3.eth.accounts.recover(message, signature)
```

```js:ethers-verify.js
const digest = ethers.utils.hashMessage(message)
const actual = ethers.utils.recoverAddress(digest, signature)
```

ご覧のとおり署名と検証の手順は両者であまり違いがないのでどちらを使っても良いと思います。
