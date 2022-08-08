---
title: "ERC-20トークンを発行する方法"
emoji: "🪙"
type: "tech"
topics: ["ethereum", "erc20", "solidity", "javascript", "hardhat"]
published: true
---

## この記事について

この記事では[Hardhat](https://hardhat.org/)を使ってEthereumのテストネットである[Goerli](https://goerli.etherscan.io/)上でERC-20トークンを発行する方法について紹介します。内容についてはAlchemy Documentationの[How to Create an ERC-20 Token](https://docs.alchemy.com/docs/how-to-create-an-erc-20-token-4-steps)チュートリアルをベースにしています。この記事の関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/how-to-create-your-own-erc20-token/tree/main)
- [発行したトークンのEtherscanページ](https://goerli.etherscan.io/token/0x662d97b8648a7BAD2923fd14c55C26Adc7418743)

https://goerli.etherscan.io/token/0x662d97b8648a7BAD2923fd14c55C26Adc7418743


## 秘密鍵の作成

[Mumbaiにスマートコントラクトをデプロイする方法](https://zenn.dev/tatsuyasusukida/articles/mumbai-smart-contract)の記事の[秘密鍵の作成](https://zenn.dev/tatsuyasusukida/articles/mumbai-smart-contract#%E7%A7%98%E5%AF%86%E9%8D%B5%E3%81%AE%E4%BD%9C%E6%88%90)セクションを参考にして秘密鍵を作成します。



## GoerliETHの入手

[Goerli Faucet](https://goerlifaucet.com/)のページにアクセスしてウォレットのアドレスをクリックしてからSend Me ETHボタンをクリックします。

1〜2分後にMetaMaskで残高が増えていることを確認できます。なお、2022年8月7日の時点では0.25 GoerliETHをもらえました。

### 参考画像

![](/images/articles/how-to-create-your-own-erc20-token/img-faucet-01.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-faucet-02.jpg)



## Appの作成

Alchemyのアカウントをお持ちでない場合は[Signup](https://auth.alchemyapi.io/signup)ページからユーザー登録を行います。

Alchemyの[ダッシュボード](https://dashboard.alchemyapi.io/)にアクセスしてページ右上のCREATE APPボタンをクリックします。

Create Appモーダルが表示されたら、下記の内容を入力してからCREATE APPボタンをクリックします。

- NAME (例: goerli)
- DESCRIPTION (空欄)
- CHAIN: Ethereum
- NETWORK: Goerli

Appが作成されたらVIEW KEYボタンをクリックします。

HTTPSエンドポイントのURLを後の手順のために控えます。この際、Copyボタンを使うと便利です。

### 参考画像

![](/images/articles/how-to-create-your-own-erc20-token/img-app-01.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-app-02.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-app-03.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-app-04.jpg)





## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir how-to-create-your-own-erc20-token
cd how-to-create-your-own-erc20-token
mkdir contracts scripts
touch contracts/SusukidaCoin.sol scripts/deploy.js .env hardhat.config.js
npm init -y
npm install --save @openzeppelin/contracts dotenv
npm install --save-dev @nomiclabs/hardhat-ethers hardhat
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### contracts/SusukidaCoin.sol

@[gist](https://gist.github.com/tatsuyasusukida/7246f3c7fafb7e83203116bf54f6c83f?file=SusukidaCoin.sol)

### hardhat.config.js

@[gist](https://gist.github.com/tatsuyasusukida/7246f3c7fafb7e83203116bf54f6c83f?file=hardhat.config.js)

### scripts/deploy.js

@[gist](https://gist.github.com/tatsuyasusukida/7246f3c7fafb7e83203116bf54f6c83f?file=deploy.js)

### .env

下記2点を入力します。

- API_URL: 先の手順で控えたAlchemyのHTTPSエンドポイントのURL
- PRIVATE_KEY: 先の手順で作成した秘密鍵

例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/7246f3c7fafb7e83203116bf54f6c83f?file=.env.example)

なお、上記の例ではCONTRACT_ADDRESSとETHERSCAN_API_KEYが含まれていますがこの記事の作業手順では不要です。



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
Contract deployed to address: 0x662d97b8648a7BAD2923fd14c55C26Adc7418743
```



## 動作確認

MetaMaskを起動して画面の末尾付近にあるImport tokensをクリックします。

Token Contract Addressにコントラクトのアドレスを入力します。

しばらくするとToken SymbolとToken Decimalが自動入力されるのでAdd Custom Tokenボタンをクリックします。

Import Tokensのページが表示されたらImport Tokensボタンをクリックします。

発行したコインの残高が表示されることを確認します。この例では1000000 SSKDが表示されています。

### 参考画像

![](/images/articles/how-to-create-your-own-erc20-token/img-check-01.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-check-02.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-check-03.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-check-04.jpg)

![](/images/articles/how-to-create-your-own-erc20-token/img-check-05.jpg)




## おわりに

この記事を書くためにベースとしたAlchemy Documentationの[How to Create an ERC-20 Token](https://docs.alchemy.com/docs/how-to-create-an-erc-20-token-4-steps)チュートリアルがnpmパッケージのインストールやhardhat.config.jsの作成などの手順を端折っていたので、適宜[Hello World Smart Contract](https://docs.alchemy.com/docs/hello-world-smart-contract)チュートリアルを参考にしながらコーディングを行いました。手順は端折られていましたが説明は丁寧だったので勉強になりました。
