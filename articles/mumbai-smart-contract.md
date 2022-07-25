---
title: "Mumbaiにスマートコントラクトをデプロイする方法【動画あり】"
emoji: "👷"
type: "tech"
topics: ["ethereum", "polygon", "solidity", "javascript", "hardhat"]
published: true
---

## この記事について

この記事ではPolygonのテストネットである[Mumbai](https://mumbai.polygonscan.com/)に[Hardhat](https://hardhat.org/)を使ってスマートコントラクトをデプロイする方法について紹介します。内容についてはAlchemy Documentationの[Hello World Smart Contract](https://docs.alchemy.com/alchemy/tutorials/hello-world-smart-contract)チュートリアルをベースにしています。この記事の関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/38db3efe12e4701998e2db3d4d2b7961)
- [準備作業の動画](https://www.youtube.com/watch?v=3J6167FdJcc)

https://www.youtube.com/watch?v=3J6167FdJcc



## 秘密鍵の作成

MetaMaskを起動してMy Accounts（画面右上にある丸いアイコン）> Create Accountをクリックします。

アカウント名を入力してからCreateボタンをクリックします。

アカウントを作成したらアドレスを後の手順のためにエディタなどにコピー＆ペーストします。アカウント名をクリックするのが便利です。

Account Options（画面右上にある3つのドットアイコン）> Account details > Export Private Keyをクリックします。

パスワードを入力してからConfirmボタンをクリックします。

秘密鍵が表示されるので後の手順ためにエディタなどにコピー＆ペーストします。

### 参考画像

![](/images/articles/mumbai-smart-contract/img-key-01.jpg)

![](/images/articles/mumbai-smart-contract/img-key-02.jpg)

![](/images/articles/mumbai-smart-contract/img-key-03.jpg)

![](/images/articles/mumbai-smart-contract/img-key-04.jpg)

![](/images/articles/mumbai-smart-contract/img-key-05.jpg)

![](/images/articles/mumbai-smart-contract/img-key-06.jpg)

![](/images/articles/mumbai-smart-contract/img-key-07.jpg)

![](/images/articles/mumbai-smart-contract/img-key-08.jpg)



## MATICの入手

[Polygon Faucet](https://faucet.polygon.technology/)のページにアクセスして下記の内容を入力してからSubmitボタンをクリックします。

- Network: Mumbai
- Select Token: MATIC Token
- Wallet Address: アカウントのアドレス

確認モーダルが表示されたらConfirmボタンをクリックします。

1〜2分後にMetaMaskで残高が増えていることを確認できます。なお、2022年7月25日の時点では0.2 MATICをもらえました。

### 参考画像

![](/images/articles/mumbai-smart-contract/img-matic-01.jpg)

![](/images/articles/mumbai-smart-contract/img-matic-02.jpg)

![](/images/articles/mumbai-smart-contract/img-matic-03.jpg)

![](/images/articles/mumbai-smart-contract/img-matic-04.jpg)

![](/images/articles/mumbai-smart-contract/img-matic-05.jpg)



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir mumbai-smart-contract
cd mumbai-smart-contract
mkdir contracts scripts
npm init -y
npm install --save dotenv
npm install --save-dev @nomiclabs/hardhat-ethers @nomiclabs/hardhat-etherscan ethers hardhat
touch .env contracts/HelloWorld.sol scripts/deploy.js scripts/interact.js hardhat.config.js
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### contracts/HelloWorld.sol

@[gist](https://gist.github.com/tatsuyasusukida/38db3efe12e4701998e2db3d4d2b7961?file=HelloWorld.sol)

### hardhat.config.js

@[gist](https://gist.github.com/tatsuyasusukida/38db3efe12e4701998e2db3d4d2b7961?file=hardhat.config.js)

### scripts/deploy.js

@[gist](https://gist.github.com/tatsuyasusukida/38db3efe12e4701998e2db3d4d2b7961?file=deploy.js)

### scripts/interact.js

@[gist](https://gist.github.com/tatsuyasusukida/38db3efe12e4701998e2db3d4d2b7961?file=interact.js)

### .env

下記2点を入力します。

- API_URL: https://matic-mumbai.chainstacklabs.com
- PRIVATE_KEY: 先の手順で作成した秘密鍵
- CONTRACT_ADDRESS: 空欄

例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/38db3efe12e4701998e2db3d4d2b7961?file=.env.example)

なお、上記の例ではPOLYGONSCAN_API_KEYが含まれていますがこの記事の作業手順では不要です。



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

実行結果を下記に示します。

```
Contract deployed to address: 0x3B035c975Ab7053479C012bce13523c931FD20E3
```

デプロイされたスマートコントラクトのアドレスを.envのCONTRACT_ADDRESSにコピー＆ペーストします。



## 動作確認

下記のコマンドを実行してスマートコントラクトのデータを読み書きします。

```shell
npx hardhat run scripts/interact.js
```

実行結果を下記に示します。

```
The message is: Hello World!
Updating the mesaage...
The message is: Hello World!!
```

コントラクトへのデータの書き込みには数十分程度のとても長い時間が必要になる場合があります。何でだろう...



## おわりに

最初はEthereumのテストネットにスマートコントラクトをデプロイしようとしたのですが、待てど暮らせどFaucetからetherをもらえないので痺れを切らしてMumbaiにデプロイした次第です。数カ月前にやった時はうまく行ったのですが、久々にやったら色々とつまづいたので自分の備忘のためにも手順をまとめておくことは重要だと感じました。



