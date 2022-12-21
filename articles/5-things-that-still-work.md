---
title: "長く役立っている学び5選"
emoji: "🖐"
type: "idea"
topics: ["git", "docker", "web", "oauth", "markdown"]
published: false
---

## この記事について

学びには賞味期限があると考えています。せっかく学ぶからにはできるだけ賞味期限が長いものを学びたいものです。この記事では過去に学んで今でも役立っていることを5つピックアップして振り返り、共通点などを探してみようと思います。

## Git

[Git](https://git-scm.com/) は言わずと知れたバージョン管理システムのデファクトスタンダードです。Gitは単体で使ってもソースコード変更履歴を管理できて便利ですが、[GitHub](https://github.com/) や [GitLab](https://gitlab.com/) を併用することで共同作業がしやすくなります。GitHubでは [GitHub Actions](https://github.com/features/actions) を利用してプルリクエスト作成やソースコードのプッシュ時に自動的にビルド／テスト／デプロイを実行することができます（ちなみに GitLab にも [GitLab CI/CD](https://docs.gitlab.com/ee/ci/) という機能があるそうです）。Git と GitHub を学ぶにはそれぞれ [Git公式ドキュメント](https://git-scm.com/book) と [GitHub公式ドキュメント](https://docs.github.com/) がおすすめです。

## Docker

[Docker](https://www.docker.com/) はコンテナイメージ作成・共有やコンテナ実行のプラットフォームです。コンテナとは簡単にいうと他から隔離されたプロセスであり、コンテナイメージをひな形として生成されます。Dockerを使うとアプリを実行環境ごとパッケージ化できるので開発環境 → テスト／本番環境へスムーズに移行できます。コンテナの運用には [Kubernetes](https://kubernetes.io/) の知識が必要になりますが [AWS Lambda](https://aws.amazon.com/lambda/) や GCP の [Cloud Run](https://cloud.google.com/run) を利用することでコンテナアプリを手軽にデプロイできます。Docker を学ぶには [Docker公式ドキュメント](https://docs.docker.com/) が一番ですが、よろしければ拙著「[図解即戦力 仮想化&コンテナがこれ1冊でしっかりわかる教科書](https://www.amazon.co.jp/dp/4297116901)」もご活用ください。

## Web技術

Webアプリ開発にはHTTP / WebSocket などの通信プロトコル、HTML / CSS / JavaScript などのプログラミング言語、JSON / XML などのデータ形式、REST / GraphQL などの API と様々な知識が望まれますが、その分だけ応用範囲が広いです。例えば [Electron](https://www.electronjs.org/) / [React Native](https://reactnative.dev/) / [Ionic Framework](https://ionicframework.com/) を使ってデスクトップ／モバイルアプリを開発できます。[React](https://reactjs.org/) の宣言的ビューの考え方は [Flutter](https://flutter.dev/) / [Jetpack Compose](https://developer.android.com/jetpack/compose) / [Swift UI](https://developer.apple.com/documentation/swiftui/) の学習にも役立ちます。Web技術を学ぶには [MDN Web Docs](https://developer.mozilla.org/ja/) と [W3C](https://www.w3.org/) がおすすめです。

## OAuth 2.0

[OAuth 2.0](https://www.ietf.org/rfc/rfc6749.txt) はアプリが API にアクセスするのを認可するしくみです。Authorization Code / Implicit / Resource Owner Password Credentials / Client Credentials の4つの付与種別（ grant type ）がありますが、Implicit は非推奨なので代わりに Authorization Code + [PKCE](https://www.ietf.org/rfc/rfc7636.txt) を使うことが推奨されます。認可に成功してアクセストークンが発行される際、データ形式として [JWT](https://jwt.io/) がよく利用されます。OAuth 2.0 と似たものとして [OpenID Connect](https://openid.net/connect/) がありますが、こちらはユーザーが誰なのか／本人かどうかを認証するためのしくみです。認証／認可は覚えることが多くて大変ですがセキュリティに直結するので手抜きできないのがつらいところです。認証／認可システムは自前で構築・運用もできますが [Auth0](https://auth0.com/) や [Firebase Authentication](https://firebase.google.com/products/auth) などのIDaaSを利用するのも便利です。認証／認可を学ぶには [Auth0公式ドキュメント](https://auth0.com/docs) やAuth屋さんの [雰囲気で使わずきちんと理解する！整理してOAuth2.0を使うためのチュートリアルガイド](https://www.amazon.co.jp/dp/B07XT8H2YG) がおすすめです。

## Markdown

## 共通点について

### 共通言語である

### 特定のプロダクトではない

### すぐには役に立たない

## おわりに