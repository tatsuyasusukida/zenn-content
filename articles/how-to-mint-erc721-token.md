---
title: "ERC-721トークンを発行する方法"
emoji: "🐵"
type: "tech"
topics: ["ethereum", "nft", "erc721", "solidity", "hardhat"]
published: true
---

## この記事について

この記事では[Hardhat](https://hardhat.org/)を使ってEthereumのテストネットである[Rinkeby](https://rinkeby.etherscan.io/)上でERC-721トークン（NFT）を発行する方法について紹介します。GoerliではなくRinkebyである理由についてはOpenSeaが2022年8月13日時点ではGoerliに対応していないためです。内容についてはAlchemy Documentationの[How to Create an NFT Tutorial](https://docs.alchemy.com/docs/how-to-create-an-nft)チュートリアルをベースにしています。この記事の関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/how-to-mint-erc721-token/tree/main)
- [発行したNFTのOpenSeaページ](https://testnets.opensea.io/assets/rinkeby/0xfb9a9a91bd6db714a9b9cd1c2514f8cec163876a/1)

https://testnets.opensea.io/assets/rinkeby/0xfb9a9a91bd6db714a9b9cd1c2514f8cec163876a/1



## 秘密鍵の作成

コントラクトをデプロイするためには秘密鍵が必要になりますが、普段使っているアカウントの秘密鍵を使うのは危険なのでテスト用のアカウントを作成します。

[Mumbaiにスマートコントラクトをデプロイする方法](https://zenn.dev/tatsuyasusukida/articles/mumbai-smart-contract)の記事の[秘密鍵の作成](https://zenn.dev/tatsuyasusukida/articles/mumbai-smart-contract#%E7%A7%98%E5%AF%86%E9%8D%B5%E3%81%AE%E4%BD%9C%E6%88%90)セクションを参考にして秘密鍵を作成します。



## RinkebyETHの入手

[Rinkeby Faucet](https://rinkebyfaucet.com/)のページにアクセスしてウォレットのアドレスをクリックしてからSend Me ETHボタンをクリックします。

1〜2分後にMetaMaskで残高が増えていることを確認できます。なお、2022年8月12日時点では0.1RinkebyETHをもらえました。

### 参考画像

![](/images/articles/how-to-mint-erc721-token/img-faucet-01.png)



## Appの作成

Alchemyのアカウントをお持ちでない場合は[Signup](https://auth.alchemyapi.io/signup)ページからユーザー登録を行います。

Alchemyの[ダッシュボード](https://dashboard.alchemyapi.io/)にアクセスしてページ右上のCREATE APPボタンをクリックします。

Create Appモーダルが表示されたら、下記の内容を入力してからCREATE APPボタンをクリックします。

- NAME (例: rinkeby)
- DESCRIPTION (空欄)
- CHAIN: Ethereum
- NETWORK: Rinkeby

Appが作成されたらVIEW KEYボタンをクリックします。

HTTPSエンドポイントのURLを後の手順のために控えます。この際、Copyボタンを使うと便利です。

### 参考画像

![](/images/articles/how-to-mint-erc721-token/img-app-01.jpg)

![](/images/articles/how-to-mint-erc721-token/img-app-02.jpg)

![](/images/articles/how-to-mint-erc721-token/img-app-03.jpg)

![](/images/articles/how-to-mint-erc721-token/img-app-04.jpg)





## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir how-to-mint-erc721-token
cd how-to-mint-erc721-token
mkdir contracts scripts
touch contracts/SusukidaNFT.sol scripts/deploy.js scripts/mint.js .env hardhat.config.js
npm init -y
npm install --save @openzeppelin/contracts
npm install --save-dev @nomiclabs/hardhat-ethers dotenv ethers hardhat
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### contracts/SusukidaNFT.sol

@[gist](https://gist.github.com/tatsuyasusukida/f45bf704cea1e404b999a26302d7fd55?file=SusukidaNFT.sol)

### hardhat.config.js

@[gist](https://gist.github.com/tatsuyasusukida/f45bf704cea1e404b999a26302d7fd55?file=hardhat.config.js)

### scripts/deploy.js

@[gist](https://gist.github.com/tatsuyasusukida/f45bf704cea1e404b999a26302d7fd55?file=deploy.js)

### scripts/mint.js

@[gist](https://gist.github.com/tatsuyasusukida/f45bf704cea1e404b999a26302d7fd55?file=mint.js)

### .env

下記4点を入力します。

- API_URL: 先の手順で控えたAlchemyのHTTPSエンドポイントのURL
- PRIVATE_KEY: 先の手順で作成した秘密鍵
- CONTRACT_ADDRESS: 空欄
- TOKEN_URI: 空欄

例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/f45bf704cea1e404b999a26302d7fd55?file=.env.example)

なお、上記の例ではETHERSCAN_API_KEYが含まれていますがこの記事の作業手順では不要です。



## コントラクトのコンパイル

下記のコマンドを実行してスマートコントラクトをコンパイルします。

```shell
npx hardhat compile
```

実行結果を下記に示します。

```
Compiled 1 Solidity file successfully
```



## コントラクトのデプロイ

下記のコマンドを実行してスマートコントラクトをデプロイします。

```shell
npx hardhat run scripts/deploy.js
```

実行結果の例を下記に示します。

```
Contract deployed to address: 0xfb9a9A91Bd6Db714a9b9Cd1c2514f8Cec163876A
https://rinkeby.etherscan.io/address/0xfb9a9A91Bd6Db714a9b9Cd1c2514f8Cec163876A
```

デプロイされたスマートコントラクトのアドレスを.envのCONTRACT_ADDRESSにコピー＆ペーストします。



## メタデータの登録

[Pinata](https://www.pinata.cloud/)のアカウントをお持ちでない場合は[Signup](https://app.pinata.cloud/)ページからユーザー登録を行います。

Pinataの[Pinmanager](https://app.pinata.cloud/pinmanager)ページにアクセスします。

UploadボタンをクリックしてNFTの画像ファイルとメタデータJSONファイルをアップロードします。例を下記に示します。

![](/images/articles/how-to-mint-erc721-token/img-metadata-01.jpg)

@[gist](https://gist.github.com/tatsuyasusukida/f45bf704cea1e404b999a26302d7fd55?file=metadata.json)

メタデータのimageには下記のURLを指定します。

```
https://gateway.pinata.cloud/ipfs/(画像ファイルのCID)
```

メタデータのURLを.envのTOKEN_URIにコピー＆ペーストします。

### 参考画像

![](/images/articles/how-to-mint-erc721-token/img-metadata-02.jpg)

![](/images/articles/how-to-mint-erc721-token/img-metadata-03.jpg)





## NFTの発行

下記のコマンドを実行してNFTを発行します。

```shell
npx hardhat run scripts/mint.js
```

実行結果を下記に示します。

```
https://rinkeby.etherscan.io/tx/0x14d05e7820577bb2b6f2f32b22e046f60739858be29f3727fec1dddf5f01dd33
```



## 動作確認

下記のURLにアクセスしてOpenSeaのアセットページを表示します。

```
https://testnets.opensea.io/assets/rinkeby/(スマートコントラクトのアドレス)/1
```

https://testnets.opensea.io/assets/rinkeby/0xfb9a9a91bd6db714a9b9cd1c2514f8cec163876a/1

発行したNFTが表示されることを確認します。

### 参考画像

![](/images/articles/how-to-mint-erc721-token/img-check-01.png)



## おわりに

OpenSeaのドキュメントにも[Creating your first NFT smart contract](https://docs.opensea.io/docs/creating-an-nft-contract)という同様のチュートリアルがあり、こちらの方がHardhatの使い方が洗練されているように見受けられるので復習する時はOpenSeaのチュートリアルに取り組んでみようと思います。
