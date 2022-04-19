---
title: "GoでCloud Runにコンテナをデプロイする方法"
emoji: "📦"
type: "tech"
topics: ["nodejs", "go"]
published: true
---

# この記事について

この記事では [Buildpacks](https://buildpacks.io/) を使って作成したGoのコンテナイメージを [Cloud Run](https://cloud.google.com/run) にデプロイする方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. コンテナイメージの作成
3. デプロイ
4. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-cloudrun-go
cd column-cloudrun-go
go mod init loremipsum.co.jp/column-cloudrun-go
touch main.go
touch makefile
```

エディタでmain.goを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/64b6bbff77e0f560a57878be2dd55cee?file=main.go)

ポイントを下記に示します。

1. "/"へのハンドラ関数を登録しています。
2. JSONレスポンスを返送するために使用する変数を定義してします。
3. JSONデータに変換してします。
4. JSONに変換する時にエラーが発生した場合は500(Internal Serve Error)応答を返送します。
5. ステータスコードを200(Success)に設定してします。
6. JSONデータを送信してします。
7. 環境変数からPORTを取得しています。
5. 環境変数PORTで指定されたポートでリクエストの待ち受けを開始します。Cloud Runはコンテナが実行されるときにCloud Runによって自動的に指定されます。その他の要件については [Container runtime contract](https://cloud.google.com/run/docs/container-contract#env-vars) のページに詳述されています。

続いてエディタでmakefileを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/64b6bbff77e0f560a57878be2dd55cee?file=makefile)

ポイントを下記に示します。

1. IMAGEはコンテナイメージの名称を示すmakefile内の変数です。
2. buildはコンテナイメージをビルドするコマンドです。[Pack](https://buildpacks.io/docs/tools/pack/) コマンドがインストールされている必要があります。
3. tagはコンテナイメージをプッシュするためにタグを付けるコマンドです。
4. pushはコンテナイメージをプッシュするコマンドです。
5. deployはコンテナをCloud Runにデプロイするコマンドです。
6. stagingはビルドからデプロイまでのコマンドをまとめたコマンドです。
7. deleteは後片付けのためのコマンドです。



# コンテナイメージの作成

下記のコマンドを実行してコンテナイメージを作成します。

```shell
make build
```

ビルドが終了すると「Successfully built image column-cloudrun-go」とメッセージが表示されます。

![ビルドのコマンドの実行結果です。メッセージの最後にSuccessfully built image column-cloudrun-goと表示されています。](/images/articles/cloud-run-go/build-01.png)



# デプロイ

下記のコマンドを実行してコンテナイメージをCloud Runにデプロイします。

```shell
make deploy
```

コマンドを実行するとService URLが表示されるので後の手順のために控えます。

![デプロイのコマンドの実行結果です。メッセージの最後にService URLが表示されます。](/images/articles/cloud-run-go/deploy-01.png)



# 動作確認

Webブラウザで先の手順で控えたService URLにアクセスして `{"ok":true}` と表示されることを確認します。

![Webブラウザでアクセスした時の様子です。{"ok":true}と表示されています。](/images/articles/cloud-run-go/check-01.png)

動作確認が完了したら下記のコマンドを実行して後片付けをします。

```shell
make delete
```

コマンドを実行すると「Do you want to continue (Y/n)? 」と削除しても良いか尋ねられるのでエンターキーを入力します。

![後片付けのコマンドを実行した直後の様子です。Do you want to continue (Y/n)? と確認メッセージが表示されます。](/images/articles/cloud-run-go/check-02.png)



# おわりに

[他の記事](https://zenn.dev/tatsuyasusukida/articles/cloud-run-nodejs) ではNode.jsのコンテナをCloud Runにデプロイする方法を紹介していますので興味のある方はご覧いただければ幸いです。

Node.jsの場合もビルドとデプロイの手順はほぼ同じであり、別の言語を使っても同じようにデプロイできる点がCloud Runの素晴らしい点だと考えています。

体感的なものですがNode.jsよりもGoの方が初回アクセスの待ち時間が短いように感じます。
ページが早く表示されることが重要な場合（多くの場合は早い方が望ましいと思いますが）はGoでの開発がおすすめです。

最後まで読んでいただきありがとうございました！
