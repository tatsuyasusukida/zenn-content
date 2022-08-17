---
title: "Webページからスマートコントラクトを読み書きする方法"
emoji: "⛓"
type: "tech"
topics: ["ethereum", "metamask", "web3", "スマートコントラクト", "nextjs"]
published: true
---

## この記事について

この記事では[ethers.js](https://docs.ethers.io/)を使ってWebページから[Goerli](https://goerli.etherscan.io/)上のスマートコントラクトを読み書きする方法について紹介します。この記事の関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/how-to-read-and-write-smart-contracts)



## 対象とするコントラクト

この記事では下記のシンプルなコントラクトを読み書き対象とします。

```sol:MyHelloWorld.sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract MyHelloWorld {
  string public message;

  constructor() {
    message = "Hello World!";
  }

  function setMessage(string memory _message) public {
    message = _message;
  }
}
```

https://goerli.etherscan.io/address/0x037f3Aed2C172baAe64792A56e9a5f84a16005fb

スマートコントラクトをデプロイする方法については下記の関連記事をご参照ください。なお、下記の記事ではGoerliではなくMumbaiを対象にしていますが手順は同様です。

- [Mumbaiにスマートコントラクトをデプロイする方法【動画あり】](https://zenn.dev/tatsuyasusukida/articles/mumbai-smart-contract)



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
npx create-next-app --typescript my-hello-world-ui
npm install --save ethers
mkdir components lib
touch components/connect.tsx
touch components/install.tsx
touch components/read.tsx
touch components/write.tsx
touch lib/abi.tsx
touch lib/create-contract.tsx
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### pages/index.tsx

@[gist](https://gist.github.com/tatsuyasusukida/2613ee1174b8dddbe26613583c7a8744?file=index.tsx)

### components/install.tsx

@[gist](https://gist.github.com/tatsuyasusukida/2613ee1174b8dddbe26613583c7a8744?file=install.tsx)

### components/connect.tsx

@[gist](https://gist.github.com/tatsuyasusukida/2613ee1174b8dddbe26613583c7a8744?file=connect.tsx)

### components/read.tsx

@[gist](https://gist.github.com/tatsuyasusukida/2613ee1174b8dddbe26613583c7a8744?file=read.tsx)

### components/write.tsx

@[gist](https://gist.github.com/tatsuyasusukida/2613ee1174b8dddbe26613583c7a8744?file=write.tsx)

### lib/create-contract.ts

@[gist](https://gist.github.com/tatsuyasusukida/2613ee1174b8dddbe26613583c7a8744?file=create-contract.ts)

### lib/abi.json

@[gist](https://gist.github.com/tatsuyasusukida/2613ee1174b8dddbe26613583c7a8744?file=abi.json)



## 動作確認

ターミナルで下記のコマンドを実行してサーバーを起動します。

```shell
npm run dev
```

ブラウザで http://localhost:3000/ にアクセスします。

Connectボタンをクリックします。

Readボタンをクリックしてメッセージが表示されることを確認します。

新しいメッセージを入力してからWriteボタンをクリックしてトランザクションが表示されることを確認します。

トランザクションが取り込まれた後、再度Readボタンをクリックして新しいメッセージが表示されることを確認します。

### 参考画像

![](/images/articles/how-to-read-and-write-smart-contracts/img-check-01.png)

![](/images/articles/how-to-read-and-write-smart-contracts/img-check-02.png)

![](/images/articles/how-to-read-and-write-smart-contracts/img-check-03.png)

![](/images/articles/how-to-read-and-write-smart-contracts/img-check-04.png)



## おわりに

この記事はAlchemyドキュメントの[NFT Minter Tutorial: How to Create a Full Stack DApp](https://docs.alchemy.com/docs/nft-minter)のチュートリアルをベースにしています。下記2点を検証したかったのでチュートリアルの内容を参考にして改変しました。

- MetaMaskを使うならAlchemyを使わなくても読み書きができること
- web3.jsではなくethers.jsを使っても読み書きができること

結果的に上記2点を検証することができました。素晴らしい情報を提供してくれるAlchemyのドキュメントには感謝しかありません。
