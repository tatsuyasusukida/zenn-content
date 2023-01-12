---
title: "bitcoinjs-lib で OP_RETURN しようぜ！"
emoji: "😎"
type: "tech"
topics: ["typescript", "bitcoin", "ブロックチェーン", "opreturn"]
published: true
---

## この記事について

OP_RETURN とは Bitcoin ブロックチェーンに任意のデータを書き込む命令です。書き込まれたデータはブロックチェーンが無くならない限り永久に存在し続けます。ロマンを感じますね。OP_RETURN については [OP_RETURN しようぜ](https://opreturn.jp/preface) が詳しいです（この記事タイトルもこちらの Web サイトからインスパイアされています）。

https://opreturn.jp/preface

この記事では TypeScript 製 Bitcoin ライブラリ の [bitcoinjs-lib](https://www.npmjs.org/package/bitcoinjs-lib) を使ってテストネットに OP_RETURN する方法を紹介します。

この記事は [GetBlock を使ってみる](https://zenn.dev/tatsuyasusukida/scraps/1943afd6008301) のスクラップをベースに作成しています。説明不足な点がありましたら恐れ入りますがスクラップを参照いただければ幸いです。



## ワークスペースの作成

ターミナルで下記のコマンドを実行してワークスペースを作成します。

```sh:ワークスペース準備コマンド
mkdir lets-op-return
cd lets-op-return
npm init -y
npm install --save-dev bitcoinjs-lib ecpair tiny-secp256k1 dotenv node-fetch ts-node @types/node @types/node-fetch
touch generate-account.ts create-transaction.ts send-transaction.ts .env
```

[ecpair](https://www.npmjs.org/package/ecpair) はキーペア（秘密鍵と公開鍵の組み合わせ）を生成するための鍵管理ライブラリです。キーペアは Bitcoin アドレスを生成するのに必要になります。過去に ecpair は bitcoinjs-lib に含まれていたようですが現在は別々のライブラリに分かれています。

[tiny-secp256k1](https://www.npmjs.org/package/tiny-secp256k1) は楕円曲線暗号ライブラリで ecpair を使うのに必要になります。

下記のパッケージについては一般的なものなので説明を割愛します。

- [dotenv](https://www.npmjs.org/package/dotenv)
- [node-fetch](https://www.npmjs.org/package/node-fetch)
- [ts-node](https://www.npmjs.org/package/ts-node)
- [@types/node](https://www.npmjs.org/package/@types/node)
- [@types/node-fetch](https://www.npmjs.org/package/@types/node-fetch)



## 秘密鍵とアドレスの生成

エディタで generate-account.ts を開いて下記の内容を入力します。

```ts:generate-account.ts
import { payments } from "bitcoinjs-lib";
import { ECPairFactory, networks } from "ecpair";
import * as ecc from "tiny-secp256k1";

const ECPair = ECPairFactory(ecc);

function main() {
  const keyPair = ECPair.makeRandom({
    network: networks.testnet,
  });

  // 秘密鍵をエクスポートします
  const wif = keyPair.toWIF();

  // アドレスをエクスポートします
  const { address } = payments.p2wpkh({ 
    pubkey: keyPair.publicKey,
    network: networks.testnet,
  });

  console.log(JSON.stringify({ wif, address }, null, 2));
}

main();
```

ターミナルで下記のコマンドを実行して秘密鍵とアドレスを生成します。

```sh:秘密鍵とアドレスの生成コマンド
npx ts-node generate-account.ts > account.json
```

実行結果の例を下記に示します。

```json:account.json
{
  "wif": "cXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  "address": "tb1XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

`wif` は WIF 形式の秘密鍵でテストネットの場合は `c` から始まります。`address` は P2WPKH 形式の Bitcoin アドレスでテストネットの場合は `tb1` から始まります。Bitcoin の秘密鍵とアドレスはいずれも形式が複数あるので紛らわしいです。これらの形式については [List of address prefixes | Bitcoin Wiki](https://en.bitcoin.it/wiki/List_of_address_prefixes) が詳しいです。



## BTC の入手

OP_RETURN に限らず Bitcoin のトランザクション発行には BTC が必要となります。テストネット用の BTC は Faucet と呼ばれるサービスから入手できます。Bitcoin の Faucet には色々ありますが今回は [Bitcoin Testnet Faucent](https://bitcoinfaucet.uo1.net/) を使います。

![Bitcoin Testnet Faucent のスクリーンショット](/images/articles/lets-op-return-with-bitcoinjs-lib/btc-01.png)

アドレスを入力して「Send」ボタンをクリックします。しばらくするとトランザクション ID が表示されるのでコピーします。[Blockstream Testnet Explorer](https://blockstream.info/testnet/) にアクセスし、コピーした ID で検索してトランザクションの内容を確認します。

![Blockstream Testnet Explorer のスクリーンショット](/images/articles/lets-op-return-with-bitcoinjs-lib/btc-02.png)

私の場合はトランザクション ID が "10b6cc612854736efb872fd5d2ff5841e7584010f0e2eedceb22b2d8165e3bf0" だったので下記の URL にアクセスしました。

https://blockstream.info/testnet/tx/10b6cc612854736efb872fd5d2ff5841e7584010f0e2eedceb22b2d8165e3bf0

トランザクションの内容はすぐに使いますのでページを開いたままにしておくことをおすすめします。また、自分のアドレスがトランザクション出力の何番目にあるのかを確認しておきます。上記のトランザクションでは私のアドレス "tb1qaenes3qmzdmn9yggh0dnr56nu2jwt4mj8dk2pn" は出力の 0 番目にあります（番号は 0 スタートです）。



## トランザクションの生成

エディタで create-transaction.ts と .env を開いて下記の内容を入力します。

```ts:create-transaction.ts
import { payments, Psbt } from "bitcoinjs-lib";
import { ECPairFactory, networks } from "ecpair";
import * as ecc from "tiny-secp256k1";

const ECPair = ECPairFactory(ecc);

function main() {
  // キーペアをインポートします
  const account = require("./account.json");
  const keyPair = ECPair.fromWIF(account.wif, networks.testnet);

  // テストネット用の PSBT（トランザクション）を作成します
  const psbt = new Psbt({ network: networks.testnet })

  // value は入手した BTC 数量、fee は支払うトランザクション手数料です
  const value = parseInt(process.env.TRANSACTION_VALUE!, 10);
  const fee = parseInt(process.env.TRANSACTION_FEE!, 10);

  // script pubkey はアドレスのようなもので locking script とも呼ばれます
  const script = payments.p2wpkh({
    pubkey: keyPair.publicKey,
    network: networks.testnet,
  }).output!;

  // トランザクション手数料を支払う UTXO を指定します
  // UTXO とは未使用のトランザクション出力で ID と 番号で指定します
  psbt.addInput({
    hash: process.env.TRANSACTION_ID!,
    index: parseInt(process.env.TRANSACTION_INDEX!, 10),
    witnessUtxo: { script, value },
  });

  // お釣りを受け取るアドレスを script pubkey で指定します
  psbt.addOutput({ script, value: value - fee });

  // OP_RETURN するデータを指定します
  psbt.addOutput({
    script: payments.embed({
      data: [Buffer.from(process.env.OP_RETURN_DATA!, 'utf-8')],
    }).output!,
    value: 0,
  });

  psbt.signInput(0, keyPair);
  psbt.validateSignaturesOfInput(0, validator);
  psbt.finalizeAllInputs();

  // トランザクション生データを 16 進数文字列で取得します
  const transaction = psbt.extractTransaction().toHex();

  process.stdout.write(transaction);
}

function validator(pubkey: Buffer, msghash: Buffer, signature: Buffer) {
  return ECPair.fromPublicKey(pubkey).verify(msghash, signature);
}

main();
```

```ini:.env
TRANSACTION_ID="10b6cc612854736efb872fd5d2ff5841e7584010f0e2eedceb22b2d8165e3bf0"
TRANSACTION_INDEX="0"
TRANSACTION_VALUE="1000"
TRANSACTION_FEE="500"
OP_RETURN_DATA="https://zenn.dev/tatsuyasusukida/articles/lets-op-return-with-bitcoinjs-lib"
```

.env については必要に応じて下記の通り変更します。TRANSACTION_ID の変更は必須です。

| 変数名 | 変更部分 | 内容 | 例 |
| ---- | ---- | ---- | ---- |
| TRANSACTION_ID | 10b6 ... 3bf0 | トランザクション ID | - |
| TRANSACTION_INDEX | 0 | Output 番号 | 0 または 1 |
| TRANSACTION_VALUE | 1000 | 入手した BTC（Satoshi単位） | 1000 |
| TRANSACTION_FEE | 500 | トランザクション手数料 | 500 |
| OP_RETURN_DATA | https ... -lib | お好きな文字列 | - |

ターミナルで下記のコマンドを実行してトランザクション生データを作成します。

```sh:トランザクション作成コマンド
npx ts-node -r dotenv/config create-transaction.ts > transaction.txt
```

私の場合は下記のトランザクション生データが作成されました。

```txt:transaction.txt
02000000000101f03b5e16d8b222ebdceee2f0104058e74158ffd2d52f87fb6e73542861ccb6100000000000ffffffff02f401000000000000160014ee6798441b1377329108bbdb31d353e2a4e5d77200000000000000004d6a4b68747470733a2f2f7a656e6e2e6465762f74617473757961737573756b6964612f61727469636c65732f6c6574732d6f702d72657475726e2d776974682d626974636f696e6a732d6c69620248304502210090075584f78f9333273de89488d32f6561de29b2f1f6dfbe9e163ac99255510c022071d7fcf29c54eb4467f638da15a7e0c666f32a18fbca7acab97e7c5add9a836e012103204cb9c091725015b525e2a958d5ed7667edb9fc5d4caf3153c97e548365414c00000000
```



## トランザクションの送信

トランザクションを送信するにはいくつかの方法があります。一例を下記に示します。

- [bitcoind](https://en.bitcoin.it/wiki/Bitcoind) を自前で構築する
- [GetBlock](https://getblock.io/) のようなノードプロバイダーサービスを利用する
- [Blockstream Esplora](https://github.com/Blockstream/esplora/blob/master/API.md) のようなサードパーティー API を利用する

今回は一番簡単な Blockstream Esplora を使います。Esplora はユーザー登録不要で無料で使えます、すごいですよね。トランザクションを送信するには https://blockstream.info/testnet/api/tx に POST リクエストを送信します。リクエストボディにはトランザクション生データを指定します。POST リクエストを送信するには下記のように curl コマンドを使います。

```sh:トランザクション送信コマンド
curl -X POST \
  -d @transaction.txt \
  -H 'content-type: text/plain;' \
  https://blockstream.info/testnet/api/tx
```

curl が無い場合はエディタで send-transaction.ts を開いて下記の内容を入力します。

```ts:send-transaction.ts
import { readFile } from "fs/promises";
import fetch from "node-fetch";

async function main() {
  const method = "POST";
  const url = "https://blockstream.info/testnet/api/tx"
  const body = await readFile("transaction.txt", "utf-8");
  const response = await fetch(url, { method, body });

  console.log(await response.text());
}

main().catch((err) => console.error(err));
```

ターミナルで下記のコマンドを実行してトランザクションを送信します。

```sh:トランザクション送信コマンド
npx ts-node send-transaction.ts
```

トランザクション送信が成功すると OP_RETURN のトランザクション ID が表示されます。私の場合は下記のトランザクション ID が表示されました。

```
e2311d097b6eff3890981b90c600f806864f41aa3192c5446d3a22f428731135
```

BlockCypher Explorer でトランザクションを確認します。

https://live.blockcypher.com/btc-testnet/tx/e2311d097b6eff3890981b90c600f806864f41aa3192c5446d3a22f428731135/

![BlockCypher Explorer のスクリーンショット](/images/articles/lets-op-return-with-bitcoinjs-lib/send-01.png)

OP_RETURN されたデータが表示され、しっかり書き込まれていることがわかります。


## おわりに

OP_RETURN は Bitcoin Core 0.9 で導入されましたが Bitcoin 自体は OP_RETURN を使って非通貨データを格納することを推奨してはいないようです。この点については [Bitcoin Core 0.9 リリースノート](https://bitcoin.org/en/release/v0.9.0#opreturn-and-data-in-the-block-chain)からも読み取れます。

> On OP_RETURN: There was been some confusion and misunderstanding in the community, regarding the OP_RETURN feature in 0.9 and data in the blockchain. This change is not an endorsement of storing data in the blockchain. The OP_RETURN change creates a provably-prunable output, to avoid data storage schemes – some of which were already deployed – that were storing arbitrary data such as images as forever-unspendable TX outputs, bloating bitcoin’s UTXO database.
> 
> Storing arbitrary data in the blockchain is still a bad idea; it is less costly and far more efficient to store non-currency data elsewhere.

推奨されていない一方で OP_RETURN はブロックチェーン証明書の [Blockcerts](https://www.blockcerts.org/) や分散IDの [ION](https://identity.foundation/ion/) にも応用されており、非通貨データをブロックチェーンに書き込むことには一定のニーズがあります。ION のようにデータの大部分を [IPFS](https://ipfs.tech/) に格納し、参照（CID）のみをブロックチェーンに書き込む方法はスマートなので他の用途でも使えそうです。

Ethereum に比べると Bitcoin は汎用性が低いですが一方で 1 つのことをうまくやっているともいえます。目的に応じて最適な手段を選べるように Bitcoin についても深く学びたいと思います。Bitcoin について学ぶには [Mastering Bitcoin](https://github.com/bitcoinbook/bitcoinbook) がおすすめです。英語なら無料で読めます、太っ腹！

https://github.com/bitcoinbook/bitcoinbook



## コメントをください

OP_RETURN の面白い応用例をご存知の方がいましたらお気軽にコメントをいただけると嬉しいです。
