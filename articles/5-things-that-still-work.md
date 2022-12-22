---
title: "長く役立っている学び5選"
emoji: "🖐"
type: "idea"
topics: ["git", "docker", "web", "oauth", "markdown"]
published: false
---

## この記事について

学びには賞味期限があると考えています。せっかく学ぶからにはできるだけ賞味期限が長いものを学びたいものです。この記事では過去に学んで今でも役立っていることを5つピックアップして紹介し、共通点などを探してみようと思います。



## Git

[Git](https://git-scm.com/) は言わずと知れたバージョン管理システムのデファクトスタンダードです。Gitは単体で使ってもソースコード変更履歴を管理できて便利ですが、[GitHub](https://github.com/) や [GitLab](https://gitlab.com/) を併用することで共同作業がしやすくなります。GitHubでは [GitHub Actions](https://github.com/features/actions) を利用してプルリクエスト作成やソースコードのプッシュ時に自動的にビルド／テスト／デプロイを実行することができます（ちなみに GitLab にも [GitLab CI/CD](https://docs.gitlab.com/ee/ci/) という機能があるそうです）。Git と GitHub を学ぶにはそれぞれ [Git公式ドキュメント](https://git-scm.com/book) と [GitHub公式ドキュメント](https://docs.github.com/) がおすすめです。



## Docker

[Docker](https://www.docker.com/) はコンテナイメージ作成・共有やコンテナ実行のプラットフォームです。コンテナとは簡単にいうと他から隔離されたプロセスであり、コンテナイメージをひな形として生成されます。Dockerを使うとアプリを実行環境ごとパッケージ化できるので開発環境 → テスト／本番環境へスムーズに移行できます。コンテナの運用には [Kubernetes](https://kubernetes.io/) の知識が必要になりますが [AWS Lambda](https://aws.amazon.com/lambda/) や GCP の [Cloud Run](https://cloud.google.com/run) を利用することでコンテナアプリを手軽にデプロイできます。Docker を学ぶには [Docker公式ドキュメント](https://docs.docker.com/) が一番ですが、よろしければ拙著[図解即戦力 仮想化&コンテナがこれ1冊でしっかりわかる教科書](https://www.amazon.co.jp/dp/4297116901)もご活用ください。



## Web 技術

Web アプリ開発には HTTP / WebSocket などの通信プロトコル、HTML / CSS / JavaScript などのプログラミング言語、JSON / XML などのデータ形式、REST / GraphQL などの API と様々な知識が望まれますが、その分だけ応用範囲が広いです。例えば [Electron](https://www.electronjs.org/) / [React Native](https://reactnative.dev/) / [Ionic Framework](https://ionicframework.com/) を使ってデスクトップ／モバイルアプリを開発できます。[React](https://reactjs.org/) の宣言的ビューの考え方は [Flutter](https://flutter.dev/) / [Jetpack Compose](https://developer.android.com/jetpack/compose) / [Swift UI](https://developer.apple.com/documentation/swiftui/) の学習にも役立ちます。Web 技術を学ぶには [MDN Web Docs](https://developer.mozilla.org/ja/) と [W3C](https://www.w3.org/) がおすすめです。



## OAuth 2.0

[OAuth 2.0](https://www.ietf.org/rfc/rfc6749.txt) は認可のしくみであり、認可とはアプリが API にアクセスする権限を付与することです。OAuth 2.0 では Authorization Code / Implicit / Resource Owner Password Credentials / Client Credentials の4つの付与種別（ grant type ）が定義されていますが、Implicit は非推奨なので代わりに Authorization Code + [PKCE](https://www.ietf.org/rfc/rfc7636.txt) を使うことが推奨されます。認可に成功してアクセストークンが発行される際、データ形式として [JWT](https://jwt.io/) がよく利用されます。OAuth 2.0 と似たものとして [OpenID Connect](https://openid.net/connect/) がありますが、こちらはユーザーが誰なのか／本人かどうかを認証するためのしくみです。認証／認可は覚えることが多くて大変ですがセキュリティに直結するので手抜きできないのがつらいところです。認証／認可システムは自前で構築・運用もできますが [Auth0](https://auth0.com/) や [Firebase Authentication](https://firebase.google.com/products/auth) などの IDaaS を利用する方が手間を省けます。認証／認可を学ぶには [Auth0公式ドキュメント](https://auth0.com/docs) やAuth屋さんの [雰囲気で使わずきちんと理解する！整理してOAuth2.0を使うためのチュートリアルガイド](https://www.amazon.co.jp/dp/B07XT8H2YG) がおすすめです。



## Markdown

[Markdown](https://daringfireball.net/projects/markdown/) は HTML を簡単に書くための記法で [Zenn](https://zenn.dev/) や [Qiita](https://qiita.com/)、GitHub の README など様々な所で利用されています。Markdown を使うと HTML のタグを書かずにリスト・図表・コードなどを表現できますが、その種類はそれほど多くないのでその分だけすぐに習得できます。ブログ記事のような比較的短いテキストを書くのに適しており、仕様書のような長いテキストを書く場合は [AsciiDoc](https://asciidoc.org/) のような多機能な記法を使う方が便利です。Markdown には様々な独自拡張（いわゆる方言）があり、[Mermaid](https://mermaid.js.org/) や [LaTeX](https://www.latex-project.org/) に対応していてフローチャートや数式を書くことができます。Markdown を学ぶには [ZennのMarkdown記法一覧] や [Markdown記法チートシート] がおすすめです。



## 共通点について

長く役立っている学びの共通点は何なのでしょうか？下記3点を思いついたのでそれぞれ少しだけ考えてみます。

- 共通言語である
- 特定のプロダクトではない
- すぐには何かを作れる訳ではない

### 共通言語である

Web 技術は例外ですが Git / Docker / OAuth 2.0 / Markdown は GCP や Zenn のような Web サービスを利用するのに学んでおく必要がある共通言語といえます。共通言語という言葉は定義があいまいですがコミュニケーションするために前提となる知識という意味だと思っています。

### 特定のプロダクトではない

Git は例外ですが Docker（コンテナ）/ Web 技術 / OAuth 2.0 / Markdown は特定のプロダクトではなく、仕様・フレームワーク・フォーマットと呼ばれる類のものです。Git もリポジトリのディレクトリ構造や[コミットオブジェクト](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)のフォーマットなどは仕様に近い性質を持っています。

### すぐには何かを作れる訳ではない

Web 技術は例外ですが Git / Docker / OAuth 2.0 / Markdown は学んでもそれ自体を使ってすぐに何かを作れる訳ではありません。ただし何かを作るのに多くの場面で使いますし、Markdownも作ったプロダクトのドキュメントを書くのに便利です。



## おわりに

個人的に「本当に役立つ学びはすぐには役に立たない」と思っており、すぐに今の仕事にいかせないテーマであっても広く深く学ぶ姿勢を大切にしたいものです。その一方でフレームワークやパッケージの使い方のようなスキルの習得もそれはそれで必要なのでバランスを大切にしたいと思います。



## コメントをください

この記事をお読みの皆さんから長く役立っている学びや共通点についてお気軽にコメントをいただけるととても嬉しいです。
