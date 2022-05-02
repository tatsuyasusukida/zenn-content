---
title: "Node.jsでSendGrid.comを使ってメールを送信する方法"
emoji: "📧"
type: "tech"
topics: ["nodejs", "sendgrid"]
published: true
---

# この記事について

この記事ではNode.jsで [SendGrid.com](https://sendgrid.com/) を利用してメールを送信する方法を紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/9d1a6867221d22250ecc3cb163a2652d)



# 前提条件

前提条件を下記に示します。

- SendGrid.comのサインアップ（ユーザー登録）が完了していること
- ドメイン認証、DKIM、SPFなど基本的な設定が完了していること



# おおまかな手順

おおまか手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir sendgrid-send-email
cd sendgrid-send-email
npm init -y
npm install --save @sendgrid/mail dotenv
touch main.js
```

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/9d1a6867221d22250ecc3cb163a2652d?file=main.js)

ポイントを下記に示します。

1. sgMail.setApiKeyメソッドを呼び出してSendGrid.comのAPIキーを設定します。
2. メールの内容（差出人、宛先、件名、本文）をオブジェクトにまとめます。差出人と宛先についてはnameとemailを使って表示名とメールアドレスを指定可能です。
3. sgMail.sendメソッドを呼び出してメールを送信しています。

## .env

エディタで.envを開いて下記3点を入力します。

- SENDGRID_API_KEY: SendGrid.comのAPIキー
- EMAIL_FROM: 差出人のメールアドレス
- EMAIL_TO: 宛先のメールアドレス

@[gist](https://gist.github.com/tatsuyasusukida/9d1a6867221d22250ecc3cb163a2652d?file=.env.example)



# 動作確認

ターミナルで下記のコマンドを実行してメールが送信されることを確認します。

```shell
node -r dotenv/config main.js
```

実行結果を下記に示します。

```
[
  {
    "statusCode": 202,
    "body": "",
    "headers": {
      "server": "nginx",
      "date": "Fri, 29 Apr 2022 02:34:56 GMT",
      "content-length": "0",
      "connection": "close",
      "x-message-id": "rBmomxZ1S_CjuF5ZZdkFrQ",
      "access-control-allow-origin": "https://sendgrid.api-docs.io",
      "access-control-allow-methods": "POST",
      "access-control-allow-headers": "Authorization, Content-Type, On-behalf-of, x-sg-elas-acl",
      "access-control-max-age": "600",
      "x-no-cors-reason": "https://sendgrid.com/docs/Classroom/Basics/API/cors.html",
      "strict-transport-security": "max-age=600; includeSubDomains"
    }
  },
  ""
]
```



# おわりに

1日100通まで無料でメールを送信できるSendGrid.comは素晴らしいサービスだと思います。1日100通を超える場合は月額$14.95からEssentialsプランが利用可能ですが、専用IPをもらえる月額$89.95のProプランの方がメールの到達率が高いのでおすすめです。なお、SendGrid.comは[構造計画研究所のSendGrid](https://sendgrid.kke.co.jp/)とは異なりますのでご留意ください。

SendGrid.comではTo、Cc、Bccで同じメールアドレスが重複していると下記のようなエラーメッセージが表示されてメールが送信できません。

```
Each email address in the personalization block should be unique between to, cc, and bcc. We found the first duplicate instance of [susukida@example.com] in the personalizations.0.bcc field.
```

その他にもメールはさまざまな理由で送信できないことがあるので下記のような工夫を行うことが望ましいです。

- リクエストの送信履歴を保存する
- リクエストを再送するためのAPIを設ける
- Cloud Schedulerなどのcronサービスを使って再送用APIを定期的にリクエストを送信する
- 送信の失敗回数が所定値を上回った場合は送信を諦めてメール送信失敗ログに記録する
- メール送信失敗ログへの記録を検出して開発者へ通知する

その他にもスパムメール扱いされないように（有効性は定かではありませんが）メールの本文に宛先の人の氏名を入れるなどの工夫が望ましいです。また、アプリ側ではSendGrid.comにリクエストを送信したことは記録できますがリクエストが処理されたかどうかはわからないので、メール送信のログについてはSendGrid.comにWebコンソールにアクセスして確認する必要があります。

これだけやってもメールは届かないことが多いので、メールが届かなかったとしても大丈夫なように例えば完了画面では確認番号などのメールと同等の内容を表示するのに加え、控えることをお願いするメッセージを表示するなどの配慮が必要になります。

メール送信って面倒ですね！この記事をお読みの方もメール送信に関するご自身のテクニックなどありましたらお気軽にコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！
