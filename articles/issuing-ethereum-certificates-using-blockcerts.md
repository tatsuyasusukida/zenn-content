---
title: "Blockcerts を使って Ethereum ブロックチェーン証明書を発行する方法"
emoji: "🪪"
type: "tech"
topics: ["blockcerts", "ethereum", "blockchain", "certificate", "gcp"]
published: true
---

## この記事について

[Blockcerts](https://www.blockcerts.org/) はブロックチェーン証明書の発行・検証のオープンソースソフトウェアで千葉工業大学が学習歴証明書を NFT で発行する時にも使用されました。

https://prtimes.jp/main/html/rd/p/000000017.000037448.html

Blockcerts のブロックチェーン証明書を発行するには [cert-issuer](https://github.com/blockchain-certificates/cert-issuer) を使いますが [Quick start](https://github.com/blockchain-certificates/cert-issuer#quick-start-using-docker) があまり親切に書かれていないので初めての人には少し大変なのではないかと思います。恥ずかしい話ですが私は1通目の証明書を発行するまでに8時間くらい時間を溶かしてしまいました。

この記事では Blockcerts をちょっと触ってみたい方が私と同じような失敗を繰り返さないように有効な Ethereum ブロックチェーン証明書を発行するまでの手順をなるべく丁寧に解説したいと思います。

この記事は下記のスクラップをベースにしていますので説明不足な部分がありましたら恐れ入りますがご参照ください。

https://zenn.dev/tatsuyasusukida/scraps/67bc1139e5410e



## 前提条件

この記事では下記4点のセットアップ手順などについての説明を割愛しています。

- [MetaMask](https://metamask.io/) がインストールされていること
- [Alchemy](https://www.alchemy.com/) のアカウントを持っていること
- [Goerli Faucet](https://goerlifaucet.com/) などからテスト用の ETH を貰っていること
- [Terraform](https://www.terraform.io/) がインストールされていること

この記事で解説する手順を試してみる場合は下記を参考にして準備をお願いします。

- MetaMask は [Chrome Web ストア](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn) からインストールできます。
- Alchemy と Goerli Faucet の使い方については [ERC-20トークンを発行する方法](https://zenn.dev/tatsuyasusukida/articles/how-to-create-your-own-erc20-token) の記事で紹介しています。
- Terraform については [Install Terraform](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli) が参考になります。Mac の場合は Homebrew を使うと下記のコマンド実行だけで簡単にインストールできます。

```sh
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```



## おおまかな流れ

手順のおおまかな流れを下記に示します。

1. インストール
    1. cert-issuer のインストール
    1. 依存関係の追加インストール
1. DID の作成
    1. ワークスペースの作成
    1. 秘密鍵のエクスポート
    1. 秘密鍵 → JWK 公開鍵へ変換
    1. DID ドキュメントの作成
    1. Profile ドキュメントの作成
    1. Cloud Storage バケットの作成
1. ブロックチェーン証明書の発行
    1. 証明書の作成
    1. 設定ファイルの作成
    1. ブロックチェーン証明書の発行
    1. ブロックチェーン証明書の検証

本題の「3. ブロックチェーン証明書の発行」よりも準備の「2. DID の作成」の方が作業ボリューム多めで大変ですので気合いの準備をお願いします。



## cert-issuer のインストール

ターミナルで下記のコマンドを実行して cert-issuer をインストールします。

```sh
pip3 install cert-issuer
```

続いて下記のコマンドを実行してインストールを確認します。

```
$ cert-issuer
usage: cert-issuer [-h] [-c MY_CONFIG] --issuing_address ISSUING_ADDRESS
                   --verification_method VERIFICATION_METHOD --usb_name
                   USB_NAME --key_file KEY_FILE
                   [--unsigned_certificates_dir UNSIGNED_CERTIFICATES_DIR]
                   [--signed_certificates_dir SIGNED_CERTIFICATES_DIR]
                   [--blockchain_certificates_dir BLOCKCHAIN_CERTIFICATES_DIR]
                   [--work_dir WORK_DIR] [--max_retry MAX_RETRY]
                   [--chain CHAIN] [--safe_mode] [--no_safe_mode]
                   [--dust_threshold DUST_THRESHOLD] [--tx_fee TX_FEE]
                   [--batch_size BATCH_SIZE]
                   [--satoshi_per_byte SATOSHI_PER_BYTE] [--bitcoind]
                   [--no_bitcoind] [--gas_price GAS_PRICE]
                   [--gas_limit GAS_LIMIT]
                   [--etherscan_api_token ETHERSCAN_API_TOKEN]
                   [--ethereum_rpc_url ETHEREUM_RPC_URL]
                   [--ropsten_rpc_url ROPSTEN_RPC_URL]
                   [--goerli_rpc_url GOERLI_RPC_URL]
                   [--sepolia_rpc_url SEPOLIA_RPC_URL]
                   [--blockcypher_api_token BLOCKCYPHER_API_TOKEN]
                   [--context_urls CONTEXT_URLS [CONTEXT_URLS ...]]
                   [--context_file_paths CONTEXT_FILE_PATHS [CONTEXT_FILE_PATHS ...]]
cert-issuer: error: the following arguments are required: --issuing_address, --verification_method, --usb_name, --key_file
```

上記のように表示されない場合は cert-issuer をフルパスで実行します。Mac の場合の例を下記に示します。

```sh
/Library/Frameworks/Python.framework/Versions/3.7/bin/cert-issuer
```

cert-issuper がどこにインストールされたかを調べるには下記のコマンドを実行します。

```
$ pip3 show cert-issuer
Name: cert-issuer
Version: 3.3.0
Summary: Issues blockchain certificates using the Bitcoin blockchain
Home-page: https://github.com/blockchain-certificates/cert-issuer
Author: Blockcerts
Author-email: info@blockcerts.org
License: MIT
Location: /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages
Requires: cert-core, cert-schema, configargparse, glob2, jsonschema, lds-merkle-proof-2019, merkletools, mock, pycoin, pyld, pysha3, python-bitcoinlib, requests, tox
Required-by:
```

上記では `Location` が /Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages なのでその周辺を調べるなどして cert-issuer の実行ファイルを探します。



## 依存関係の追加インストール

cert-issuer で Ethereum ブロックチェーン証明書を発行するには下記の依存関係を追加でインストールする必要があります。

https://github.com/blockchain-certificates/cert-issuer/blob/master/ethereum_requirements.txt

できれば Ethereum くらいはデフォルトで対応して欲しい所ですが無いものは仕方ありません。ターミナルで下記のコマンドを実行して依存関係を追加インストールします。

```sh
pip3 install -r https://raw.githubusercontent.com/blockchain-certificates/cert-issuer/master/ethereum_requirements.txt
```



## ワークスペースの作成

ターミナルで下記のコマンドを実行してワークスペースを準備します。

```sh
mkdir blockcerts
cd blockcerts
npm init -y
npm install --save-dev @trust/keyto
mkdir -p data/unsigned_certificates data/blockchain_certificates data/work
touch data/unsigned_certificates/my_certificate.json
touch conf.ini convert.js did.json key_file.txt main.tf profile.json terraform.tfvars
```

「`@trust/keyto` って必要なの？」とか「なぜ [Terraform](https://www.terraform.io/) を使うの？」とか色々と疑問があるとは思いますが後でご理解いただけると思うので少しだけご辛抱ください。



## DID について

証明書を作成するには検証に使用する公開鍵を verification method（検証方法）として指定する必要があり、Blockcerts では verification method を指定するのに [DID](https://www.w3.org/TR/did-core/) を使用します。DID は Decentralized ID のことで日本語では「分散ID」や「分散型識別子」と呼ばれます。DID を作成するには様々な方法がありますが、以下では GCP の Cloud Storage を使って `did:web` から始まる DID を作成する手順を解説します。

`did:web` から始まる DID を作成するには Web サーバーの `/.well-known/did.json` のパスで DID ドキュメントと呼ばれるファイルにアクセスできるようにします。例えば `did:web:example.com` の場合は https://example.com/.well-known/did.json の URL で DID ドキュメントを公開します。



## 秘密鍵のエクスポート

MetaMask から秘密鍵をエクスポートして key_file.txt にコピー＆ペーストします。公開鍵ではなく秘密鍵ですので取扱いには十分にご注意ください。

```txt:key_file.txt
1234567890123456789012345678901234567890123456789012345678901234
```



## 秘密鍵 → JWK 公開鍵へ変換

DID では公開鍵を [JWK](https://www.rfc-editor.org/rfc/rfc7517)（Json Web Key）で指定する必要があります。下記の Node.js コードを作成・実行して秘密鍵 → JWK 公開鍵へ変換します。

```js:convert.js
const fs = require('fs');
const keyto = require("@trust/keyto");

if (require.main === module) {
  main();
}

function main() {
  const file = process.argv[2];
  const blk = fs.readFileSync(file, 'utf-8');
  const key = keyto.from(blk, "blk");
  const jwk = key.toJwk("public");

  console.log(JSON.stringify(jwk, null, 2));
}
```

```sh
node convert.js key_file.txt
```

出力結果の例を下記に示します。

```json
{
  "kty": "EC",
  "crv": "K-256",
  "x": "oTj4PkPzlNowto09OpAU0BVJ6DJc2vg_lFoW9wiZ60A",
  "y": "D9VaE9hbsOagGab_0euWcNUaCDphfBPg-C_aWD7npx4"
}
```



## DID ドキュメントの作成

[DID ドキュメント](https://www.w3.org/TR/did-core/#dfn-did-documents)は証明書を検証するのに使う公開鍵を実際に格納するドキュメントです。エディタで did.json を開いて下記の内容を入力して DID ドキュメントを作成します。

```json:did.json
{
  "@context": ["https://www.w3.org/ns/did/v1"],
  "id": "did:web:blockcerts-20230104.storage.googleapis.com",
  "service": [
    {
      "id": "#issuer-profile",
      "type": "IssuerProfile",
      "serviceEndpoint": "https://blockcerts-20230104.storage.googleapis.com/profile.json"
    }
  ],
  "verificationMethod": [
    {
      "id": "#key-1",
      "controller": "did:web:blockcerts-20230104.storage.googleapis.com",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "publicKeyJwk": {
        "kty": "EC",
        "crv": "K-256",
        "x": "oTj4PkPzlNowto09OpAU0BVJ6DJc2vg_lFoW9wiZ60A",
        "y": "D9VaE9hbsOagGab_0euWcNUaCDphfBPg-C_aWD7npx4"
      }
    }
  ]
}
```

上記の内容については下記の通り変更する必要があります。

| パス | 変更部分 | 内容 |
| ---- | ---- | ---- |
| id | blockcerts-20230104 | Cloud Storage バケット名 |
| service[0].serviceEndpoint | blockcerts-20230104 | Cloud Storageバケット名 |
| verificationMethod[0].controller | blockcerts-20230104 | Cloud Storageバケット名 |
| verificationMethod[0].publicKeyJwk | {"kty": ... npx4"} | JWK 公開鍵 |

Cloud Storage バケット名には例えば "blockcerts-" + 日付8桁を使います（バケットについては後から作成します）。JWK 公開鍵には `node convert.js key_file.txt` の出力結果を使います。



## Profile ドキュメントの作成

[Profile ドキュメント](https://w3id.org/openbadges#Profile) は証明書発行者のプロフィールをまとめたドキュメントで DID ドキュメントの `serviceEndpoint` から参照されます。エディタで profile.json を開いて下記の内容を入力して Profile ドキュメントを作成します。

```json:profile.json
{
  "@context": [
    "https://w3id.org/openbadges/v2",
    "https://w3id.org/blockcerts/v3"
  ],
  "id": "https://blockcerts-20230104.storage.googleapis.com/profile.json",
  "type": "Profile",
  "name": "Lorem Ipsum Co. Ltd.",
  "url": "https://www.loremipsum.co.jp",
  "email": "blockcerts@loremipsum.co.jp",
  "publicKey": [
    {
      "id": "ecdsa-koblitz-pubkey:0x1234567890123456789012345678901234567890"
    }
  ]
}
```

上記の内容については下記の通り変更する必要があります。

| パス | 変更部分 | 内容 |
| ---- | ---- | ---- |
| id | blockcerts-20230104 | Cloud Storage バケット名 |
| publicKey[0].id | 0x1234 ... 7890 | Ethereum 公開鍵 |

Cloud Storage バケット名には DID ドキュメントと同じものを使います。Ethereum 公開鍵については MetaMask からコピー＆ペーストします。こちらは秘密鍵ではなく公開鍵ですので間違えないようにご留意ください。



## Cloud Storage バケットの作成

DID + Profile ドキュメント公開用の Cloud Storage バケットを Terraform を使って作成します。エディタで main.tf と terraform.tfvars を開いてそれぞれ下記の内容を入力します。

```tf:main.tf
variable "project" {}
variable "bucket_name" {}
variable "bucket_location" {}

provider "google" {
  project = var.project
}

resource "google_storage_bucket" "my_bucket" {
  name                        = var.bucket_name
  location                    = var.bucket_location
  force_destroy               = true

  cors {
    origin          = ["*"]
    method          = ["GET"]
    response_header = ["Content-Type"]
    max_age_seconds = 30
  }
}

resource "google_storage_bucket_iam_binding" "public_rule" {
  bucket = google_storage_bucket.my_bucket.name
  role = "roles/storage.legacyObjectReader"
  members = [
    "allUsers",
  ]
}

resource "google_storage_bucket_object" "did" {
  bucket = google_storage_bucket.my_bucket.name
  name   = ".well-known/did.json"
  source = "did.json"
  cache_control = "public, max-age=30"
}

resource "google_storage_bucket_object" "profile" {
  bucket = google_storage_bucket.my_bucket.name
  name   = "profile.json"
  source = "profile.json"
  cache_control = "public, max-age=30"
}
```

```ini:terraform.tfvars
project         = "xxxxxxxx"
bucket_name     = "loremipsumcojp-cert-issuer-20221229"
bucket_location = "ASIA-NORTHEAST1"
```

main.tf についてはそのままで OK ですが terraform.tfvars については下記の通り変更する必要があります。

| パス | 変更部分 | 内容 |
| ---- | ---- | ---- |
| project | xxxxxxxx | GCP のプロジェクト ID |
| bucket_name | xxxxxxxx | Cloud Storage バケット名 |

GCP のプロジェクト ID についてはターミナルで `gcloud config get-value project` 実行して取得できます。Cloud Storage バケット名には DID + Profile ドキュメントと同じものを使います。terraform.tfvars を変更したらターミナルで下記のコマンドを実行してバケットを作成します。

```sh
terraform init
terraform apply
```

バケットを作成したら DID + Profile ドキュメントの URL にアクセスできるかを確認します。URL の例を下記に示します。

- https://blockcerts-20230104.storage.googleapis.com/.well-known/did.json
- https://blockcerts-20230104.storage.googleapis.com/profile.json



## 証明書の作成

以上で DID の作成が終わったのでようやく証明書を作成できます。証明書を作成するにはエディタで data/unsigned_certificates/my_certificate.json を開いて下記の内容を入力します。

```json:data/unsigned_certificates/my_certificate.json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/blockcerts/v3"
  ],
  "id": "urn:uuid:bbba8553-8ec1-445f-82c9-a57251dd731c",
  "type": [
    "VerifiableCredential",
    "BlockcertsCredential"
  ],
  "issuer": "did:web:blockcerts-20230104.storage.googleapis.com",
  "issuanceDate": "2023-01-04T00:00:00Z",
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21"
  }
}
```

上記の内容については下記の通り変更する必要があります。

| パス | 変更部分 | 内容 |
| ---- | ---- | ---- |
| id | blockcerts-20230104 | Cloud Storage バケット名 |

作成した証明書に対して cert-issuer を使って署名 + 署名データをブロックチェーンに書き込むことでブロックチェーン証明書が発行されます。



## 設定ファイルの作成

cert-issuer への入力をまとめた設定ファイルを作成します。エディタで conf.ini を開いて下記の内容を入力します。

```ini:conf.ini
issuing_address = 0x1234567890123456789012345678901234567890
verification_method = did:web:blockcerts-20230104.storage.googleapis.com#key-1
usb_name=.
key_file=key_file.txt

unsigned_certificates_dir=/Users/susukida/workspace/blockcerts/data/unsigned_certificates
blockchain_certificates_dir=/Users/susukida/workspace/blockcerts/data/blockchain_certificates
work_dir=/Users/susukida/workspace/blockcerts/data/work

chain = ethereum_goerli
goerli_rpc_url = https://eth-goerli.g.alchemy.com/v2/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

no_safe_mode
```

上記の内容については下記の通り変更する必要があります。

| パス | 変更部分 | 内容 |
| ---- | ---- | ---- |
| issuing_address | 0x1234 ... 7890 | Ethereum 公開鍵 |
| verification_method | blockcerts-20230104 | Cloud Storageバケット名 |
| unsigned_certificates_dir | /Users/ ... /blockcerts/ | 作業ディレクトリ |
| blockchain_certificates_dir | /Users/ ... /blockcerts/ | 作業ディレクトリ |
| work_dir | /Users/ ... /blockcerts/ | 作業ディレクトリ |
| goerli_rpc_url | xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx | Alchemy の API キー |

goerli_rpc_url については設定しない場合はデフォルトのエンドポイントが使われますが、混雑状況によっては時間がかかったり失敗したりすることがあります。少し面倒ですが Alchemy のエンドポイントを使う方がおすすめです。



## ブロックチェーン証明書の発行

ターミナルで下記のコマンドを実行してブロックチェーン証明書を発行します。

```sh
cert-issuer -c conf.ini
```

コマンド実行結果の例を下記に示します。

```
WARNING - Your app is configured to skip the wifi check when the USB is plugged in. Read the documentation to ensure this is what you want, since this is less secure
INFO - This run will try to issue on the ethereum_goerli chain
INFO - Set cost constants to recommended_gas_price=20000000000.000000, recommended_gas_limit=25000.000000
INFO - Processing 1 certificates
INFO - Processing 1 certificates under work path=/Users/susukida/workspace/blockcerts/data/work
INFO - Getting balance with EthereumRPCProvider: 198279040000000000
INFO - Total cost will be 500000000000000 wei
INFO - Starting finalizable signer
WARNING - app is configured to skip the wifi check when the USB is plugged in. Read the documentation to ensure this is what you want, since this is less secure
INFO - Stopping finalizable signer
WARNING - app is configured to skip the wifi check when the USB is plugged in. Read the documentation to ensure this is what you want, since this is less secure
INFO - here is the op_return_code data: f4906de7db4f9a826b5998472f51eaac762d429b29b6c2496f38870a477929d9
INFO - Fetching nonce with EthereumRPCProvider
INFO - Starting finalizable signer
WARNING - app is configured to skip the wifi check when the USB is plugged in. Read the documentation to ensure this is what you want, since this is less secure
INFO - Stopping finalizable signer
WARNING - app is configured to skip the wifi check when the USB is plugged in. Read the documentation to ensure this is what you want, since this is less secure
INFO - signed Ethereum trx = f884048504a817c8008261a894deaddeaddeaddeaddeaddeaddeaddeaddeaddead80a0f4906de7db4f9a826b5998472f51eaac762d429b29b6c2496f38870a477929d92da01b0801ad15681457103aff952fc6a1a6b327b8eb1840455c37fec07609a6cccca068922ac353d38527ed5c5c68a02d73c740cc95efd1d0a75172af29317cb32af5
INFO - verifying ethDataField value for transaction
INFO - verified ethDataField
INFO - Broadcasting transaction with EthereumRPCProvider
INFO - Broadcasting succeeded with method_provider=<cert_issuer.blockchain_handlers.ethereum.connectors.EthereumRPCProvider object at 0x7fc1e0d04e50>, txid=0xca8732ae20f0e67c8091af0b83055f5e21a32776c5df7f15fa2da25743320a7e
INFO - merkle_json: {'path': [], 'merkleRoot': 'f4906de7db4f9a826b5998472f51eaac762d429b29b6c2496f38870a477929d9', 'targetHash': 'f4906de7db4f9a826b5998472f51eaac762d429b29b6c2496f38870a477929d9', 'anchors': ['blink:eth:goerli:0xca8732ae20f0e67c8091af0b83055f5e21a32776c5df7f15fa2da25743320a7e']}
INFO - Broadcast transaction with txid 0xca8732ae20f0e67c8091af0b83055f5e21a32776c5df7f15fa2da25743320a7e
INFO - Your Blockchain Certificates are in /Users/susukida/workspace/blockcerts/data/blockchain_certificates
```

発行されたブロックチェーン証明書は data/blockchain_certificates/my_certificate.json に出力されます。出力される内容の例は下記の通りで `proof` フィールドが追加されたことがわかります。

```json:data/blockchain_certificates/my_certificate.json
{
  "@context": [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3id.org/blockcerts/v3"
  ],
  "id": "urn:uuid:bbba8553-8ec1-445f-82c9-a57251dd731c",
  "type": [
    "VerifiableCredential",
    "BlockcertsCredential"
  ],
  "issuer": "did:web:blockcerts-20230104.storage.googleapis.com",
  "issuanceDate": "2023-01-04T00:00:00Z",
  "credentialSubject": {
    "id": "did:example:ebfeb1f712ebc6f1c276e12ec21"
  },
  "proof": {
    "type": "MerkleProof2019",
    "created": "2023-01-04T00:00:00.000000",
    "proofValue": "z7veGu1qoKR3AS5M3xfNxYMVGUCxFzaEQ5NkRWDGTowFPyL2gB7vtCVDfK2e4oETN19HnnqmXL3CS2qpMgnWe2XUHCVN7ufHArBc54QVVk2XouWzakWMU83iHnAsk186DuvJv5vLXN2p9bFXRcwFTfqxkyzDL9E8G8CEZ43X9HnFNz6Yz38U4ypGt6XbmKM7EnLTK5NaKRkHrQehPyRfFCFhjBEhgdT9QTHf56PxwqmyF7Q8Gwf3MEZwbu5SNst58qSvRFQch7zaW1ZDw85Zqk1uMGJBwomRnwPtgmaKknR6rn3Pd4FMYp",
    "proofPurpose": "assertionMethod",
    "verificationMethod": "did:example:1234"
  }
}
```



## ブロックチェーン証明書の検証

[Blockcerts](https://www.blockcerts.org/) の Web サイトへアクセスして Certificate URL テキストボックスの下にある「Choose JSON file」リンクをクリックします。

![](/images/articles/issuing-ethereum-certificates-using-blockcerts/verify-01.png)

ファイル選択ダイアログが表示されたら先ほど発行したブロックチェーン証明書のJSONファイルを選択します。ファイルを選択すると自動的に検証が開始し、検証に成功すると「この証明書は有効な Ethereum Testnet 証明書です。」とメッセージが表示されます。

![](/images/articles/issuing-ethereum-certificates-using-blockcerts/verify-02.png)



## おわりに

冒頭で紹介した千葉工業大学の事例では下記のステップで学習歴証明書を NFT として発行しています。

1. ブロックチェーン証明書を発行して IPFS に保存
2. IPFS の CID を含む URL を NFT メタデータの `external_url` として記録
3. NFT を発行して学生の MetaMask アカウントへ送信

NFT（ERC-721トークン）を発行する方法については下記の記事で紹介していますので興味がありましたらお読みください。下記の記事は古いので Rinkeby を使っていますが非推奨なので代わりに Goerli を使ってください。

https://zenn.dev/tatsuyasusukida/articles/how-to-mint-erc721-token

証明書と NFT の発行にはそれぞれ別のブロックチェーンを使用して大丈夫なので例えば証明書には Bitcoin、NFT には Ethereum ということもできます。千葉工業大学の事例では証明書には Ethereum、NFT には Polygon を使っています。この記事では Ethereum 証明書を発行しましたが機会があれば [GetBlock](https://getblock.io/) を使って Bitcoin 証明書を発行する記事を書いてみたいと思います。

最後になりますがこの記事を作成するにあたって [PitPa](https://zenn.dev/p/sakazuki_xyz) さんの [nft-vc](https://github.com/pitpa/nft-vc) のリポジトリにとても助けられたので下記にリンクを貼ります。このリポジトリが無ければ途中で挫折してしまっていたと思うのでこの場を借りて深く感謝申し上げます。

https://github.com/pitpa/nft-vc


