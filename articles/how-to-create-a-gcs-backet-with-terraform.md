---
title: "TerraformでCloud Storageバッケットを作成する方法"
emoji: "🪣"
type: "tech"
topics: ["gcp", "terraform"]
published: true
---

## この記事について

この記事では[Terraform](https://www.terraform.io/)を使ってCloud Storageのバケットを作成する方法について紹介します。



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```sh
touch main.tf terraform.tfvars
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### main.tf

@[gist](https://gist.github.com/tatsuyasusukida/e27977c892fed4f55d6f30c3c0570eca?file=main.tf)

### terraform.tfvars

@[gist](https://gist.github.com/tatsuyasusukida/e27977c892fed4f55d6f30c3c0570eca?file=terraform.tfvars.example)

プロジェクトIDについては下記のコマンドで一覧できます。

```
gcloud config configurations list
```

バケット名の00000000の部分は日付8桁などに置き換えます。



## バケットの作成

必要に応じて下記のコマンドを実行してtfファイルのフォーマットやバリデーションを行います。

```sh
terraform fmt
terraform validate
```

ターミナルで下記のコマンドを実行してバケットを作成します。

```sh
terraform init
terraform apply
```

作成されたバケット名を確認するには下記のコマンドを実行します。

```sh
terraform output
```



## 参考にしたWebページ

- [Build Infrastructure - Terraform GCP Example](https://learn.hashicorp.com/tutorials/terraform/google-cloud-platform-build?in=terraform/gcp-get-started)
- [google_storage_bucket | Resources | hashicorp/google | Terraform Registry](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/storage_bucket)



## おわりに

`terraform output -raw bucket_name`とコマンドを実行するとTerraformの出力値（Output Values）を取得することができるのできます。このコマンドと環境変数を組み合わせることでプログラムからTerraformの出力値を参照することができます。一例を下記に示します。

```sh
MY_BUCKET=`terraform output -raw bucket_name` \
  node -e 'console.log(process.env.MY_BUCKET)'
```

上記のようなやり方はあまり一般的ではないので使用は推奨されません。特にTerraformの出力値の数が多い場合はコマンドが長くなって読みにくくなります。少し面倒ですがTerraformの出力値を.envなどに手動でコピーするのがシンプルなので良いと考えています。
