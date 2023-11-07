---
title: "Box API について調査した 3 週間を振り返る"
emoji: "📦"
type: "tech"
topics: ["box", "java", "kotlin", "oauth", "webhook"]
published: true
publication_name: "collabostyle"
---

## この記事について

ここ 3 週間ほど仕事で Box API について調査する機会をいただきました。この記事では Box API の基本的な使い方を中心に調査で得られた知見を共有したいと思います。また、Box API を使う上での注意点やトラブルシューティングの方法についても言及します。この記事が Box API を使ったプロジェクトや開発に役立てば幸いです。

## Box とは何か？

Box はクラウド上でファイルを管理・共有するためのオンラインストレージサービスであり、企業向けのプラットフォームとして有名です。Dropbox や Google Drive など類似サービスと比較すると企業向けの機能が充実しており、セキュリティ面でも優れています。

Box を利用することでユーザーはファイルをオンライン上で保存し、必要な場合には共有することができます。また、Box のユーザーはパソコン・スマートフォン・タブレットなど様々なデバイスからファイルにアクセスすることができます。

少し古いですが下記の記事によると顧客企業数は世界で 95,000 社を超え、Fortune 500 の 69 % が Box を導入しており、日本でも日経 225 に含まれる企業の 50% が Box の顧客企業とのことです。

https://www.itmedia.co.jp/news/articles/2003/02/news029.html

Box のサービスはアプリを通じて利用できますが Box API を利用することでプログラムから Box 上のファイルを操作することができます。Box API を使うことで Box をより便利に活用し、より簡単にカスタマイズすることができます。

## Box API とは何か？

Box API は Box 上のファイルやフォルダーをプログラムから操作するための RESTful API です。一部の例外を除いてデータのやり取りには JSON 形式が使用されます。Box API には様々なエンドポイントが用意されており、フォルダーの作成・一覧・共有、ファイルのアップロード・ダウンロード・削除などの操作が可能です。

https://ja.developer.box.com/reference/

Box API にはユーザー認証（OAuth 2.0）とサーバー認証が用意されており、ユーザー認証の場合は連携アプリから API を利用する前にエンドユーザーからアクセス許可を得る必要があります。一方、サーバー認証の場合はユーザー組織の管理者が連携アプリを承認すればエンドユーザーによる操作は必要ありません。

https://ja.developer.box.com/guides/authentication/select/

Box API はそのまま利用することもできますが CLI や SDK を使うことでより簡単に利用できます。この記事の作成時点では下記 6 つのプラットフォーム向けの SDK が用意されています。

- Java
- .NET
- Python
- Node.js
- iOS
- Android(※)

※ Android SDK は 2023 年 6 月 1 日にサポートが終了します。それ以降は Java SDK を使用することが推奨されます。

## 調査の概要

調査の目的は Box API を実際に使ってみて概要や利用方法を学び、Box API を活用したアプリを開発する上での知見を記録・共有することです。

調査の範囲については Box API の基本的な機能に焦点を当て、Java SDK を使ってコードを実装して検証する所までを行いました。

調査した内容については下記 9 点です。

- Box API Java SDK の基本的な使い方
- Kotlin から Java SDK を利用する方法
- Business プランの無料トライアルの始め方
- サーバー認証（CCG: クライアント資格情報許可）
- サーバー認証（JWT: JSON Web Token）+ ユーザーの代理アクセス
- Java8 でサーバー認証（JWT）
- サービスアカウント → ユーザーへのファイル共有
- Box CLI の使い方
- Webhook

この他にも Java で Web サーバーを作成する必要があったので Spring Boot についても調査しました。

調査を進めるために参考にした情報源については [Box API 公式ガイド](https://ja.developer.box.com/guides/)と [Box API Java SDK の GitHub リポジトリ](https://github.com/box/box-java-sdk) の主に 2 つです。基本的には前者の公式ガイドをベースに読み進め、技術的な細かい部分を知りたい場合に後者の GitHub リポジトリを参照しました。

調査の期間は 2023/4/14(金)〜5/3(水) までの約 3 週間で、稼働日数と時間の合計はそれぞれ 16 日間と 37 時間でした。

記録については依頼者の方から許可をいただいた上で Zenn スクラップに投稿しました。以下、調査の成果物として作成した Zenn スクラップについてを紹介します。

## Box API Java SDK

https://zenn.dev/tatsuyasusukida/scraps/0293205105d96c

このスクラップでは Box アカウントの作成から Box API Java SDK を使ってフォルダー作成やファイルアップロードを行うまでの一連の流れを解説しています。具体的には下記のトピックをカバーしています。

- Box アカウントの作成
- カスタムアプリの作成
- 開発者トークンの使用
- Box API Java SDK のインストール
- ルートフォルダーの一覧表示
- フォルダーの作成
- ファイルのアップロード
- ユーザー認証（OAuth 2.0）

## Kotlin から SDK 利用

https://zenn.dev/tatsuyasusukida/scraps/8ec67c09a44078

このスクラップでは Kotlin から Box API Java SDK を利用してルートディレクトリの中身を一覧する方法を解説しています。具体的には下記のトピックをカバーしています。

- IntelliJ IDEA での Kotlin プロジェクトの作成
- プロジェクト作成直後に main() 関数が実行できない原因
- main() 関数を実行するために build.gradle.kts を修正する方法
- 開発者トークンを使用したルートフォルダーの一覧表示

## Business プラン無料トライアル

https://zenn.dev/tatsuyasusukida/scraps/42155b55231f4b

このスクラップでは Box の Web サイトから Business プランの無料トライアルを申し込む手順を解説しています。

## サーバー認証（CCG）

https://zenn.dev/tatsuyasusukida/scraps/92690df756d575

このスクラップではサーバー認証（クライアント資格情報許可）を使ってカスタムアプリの作成・承認を行い、カスタムアプリとユーザーの間でファイル共有（Box では「コラボレーション」と呼びます）を行うまでの一連の流れを解説しています。具体的には下記のトピックをカバーしています。

- 開発者によるカスタムアプリの作成・設定
- 管理者によるカスタムアプリの承認
- ユーザータイプとサービスアカウントについて
- ユーザー → サービスアカウントへのファイル共有

## サーバー認証（JWT）

https://zenn.dev/tatsuyasusukida/scraps/85b8cd22880306

このスクラップではサーバー認証（JWT）を使ってカスタムアプリの作成・承認を行い、カスタムアプリからユーザーの代理としてアクセスを行う方法を解説しています。Java 8 ではできなかったので Java 17 を使用しました。具体的には下記のトピックをカバーしています。

- 公開／秘密キーペアの生成
- JSON 形式の構成ファイルの読み込み
- SLF4J（ログ収集）の依存関係の追加
- as-user ヘッダーによるユーザーの代理アクセス
- カスタムアプリのスコープ設定時の注意点

## Java 8 で JWT

https://zenn.dev/tatsuyasusukida/scraps/5ead39521d496c

このスクラップでは Java 8 でサーバー認証（JWT）を成功させるために `JAVA_HOME` の JAR を書き換える方法について解説しています。具体的には下記のトピックをカバーしています。

- Java 8 でサーバー認証（JWT）が失敗する理由
- JCE に関する JAR ファイルの入手方法
- JAR ファイルの書き換え作業
- as-user ヘッダーによるユーザーの代理アクセス

## ファイル共有

https://zenn.dev/tatsuyasusukida/scraps/7f9568c58b3692

このスクラップではサーバー認証を使っているカスタムアプリとユーザーの間でファイルをやり取りするためにサービスアカウント → ユーザーにファイル共有（コラボレーション）を行う方法について解説しています。ファイル共有をする際には対象ユーザーのメールアドレスか ID を指定しますが、このスクラップではメールアドレスを使用しました。

## Box CLI

https://zenn.dev/tatsuyasusukida/scraps/e640bec9111db1

このスクラップでは Box CLI を使えるようにするまでのセットアップ手順に加え、フォルダー作成などの Box CLI の基本的な使い方について解説しています。ユーザー認証（OAuth 2.0）だけではなくサーバー認証（クライアント資格情報許可）を設定する方法についても扱っています。

## Webhook

https://zenn.dev/tatsuyasusukida/scraps/a4be06cdec74e4

このスクラップでは Box API の Webhook 機能を使ってファイルアップロードなどのイベントがあった時に HTTP で通知を受け取る方法について解説しています。実際に Cloud Run に Spring Boot アプリをデプロイして Webhook を受け取るところまでを検証しました。具体的には下記のトピックをカバーしています。

- Webhook V1 と V2 の違い
- Cloud Run で Java 17 を指定する方法
- Webhook V2 の作成・設定
- なりすまし防止のための署名の検証

## おまけ：Spring Boot

https://zenn.dev/tatsuyasusukida/scraps/48f993dfc438d9

https://zenn.dev/tatsuyasusukida/scraps/93139f246e2eb2

これらのスクラップでは Java / Kotlin を使って Spring Boot で Web アプリを作る方法について解説しています。ユーザー認証（OAuth 2.0）や Webhook を検証するためには Web アプリを作成する必要があったのですが、その手順を同じスクラップにまとめるとボリュームが膨らみそうだったので専用のスクラップを設けた次第です。

## まとめのまとめ

それぞれのスクラップには「まとめ」セクションを設けて得られた知見をまとめているのですが、特に強調しておきたいと感じた点をピックアップして下記に示します。

- ファイルアップロードには分割と非分割の 2 種類があり、分割アップロードはファイルサイズが 2 MB 以上でないと利用できない。
- サーバー認証を試すには Business プラン以上が必要になる。
- ユーザーの代理アクセスにはアプリアクセスレベルをアプリ + Enterprise アクセスに設定する必要がある。
- アプリアクセスレベルを変更すると連動して他の設定が上書きされるので注意する。
- 管理コンソールで承認する時に権限設定が反映されていないことがあるので注意する。
- ユーザーの代理アクセスは設定が正しければクライアント資格情報許可でも行える。
- Java 8 で JWT 認証を行うには Oracle から JCE Unlimited Strength Jurisdiction Policy Files 7 をダウンロードして JAVA_HOME/lib/security に JAR ファイル 2 件をインストールする必要がある。

## わからなかったこと

[Box の価格ページ](https://www.box.com/ja-jp/pricing) のプランの内容セクションに「エンタープライズアプリの統合」という項目があるのですがいまだに何なのか謎です。

![](https://storage.googleapis.com/zenn-user-upload/002aa5a6cc00-20230421.png)

サーバー認証を使用するカスタムアプリの最大登録件数なのかと思いましたが Business プランでも問題なく 2 件以上登録できたので違うようです。ご存じの方がいましたらコメントで教えていただけると嬉しいです。

## おわりに

この記事では Box API を利用する方法についてまとめたスクラップを紹介しました。

スクラップの作成を通じて Box API を利用するための基礎知識を身につけることができました。今後はこれらの知識を実際のプロジェクトや開発に応用し、経験を積んでいきたいと思います。

最後にこの記事をお読みいただきありがとうございました。今後も Box API に関する新しい知見を得たらスクラップや記事にまとめて積極的に発信したいと思います。

## お気軽にコメントをください

記事の内容に関して質問や不明な点がありましたらコメントをいただければ僕の知っている範囲で回答させてもらいます。間違っている点の指摘やアドバイスについてもいただければとても感謝します。シンプルに「良かったよ」などの感想も大歓迎です、とても励みになります。何かありましたらお気軽にコメントをいただければ嬉しいです。