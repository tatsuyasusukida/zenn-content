---
title: "Node.jsでwinstonを使ってログを収集する方法"
emoji: "🪵"
type: "tech"
topics: ["nodejs", "winston", "log"]
published: false
---

## この記事について

この記事ではNode.jsで [winston](https://www.npmjs.com/package/winston) を使ってログを収集する方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/92b0f5dd8523ebdf19edb4a977a8030a#file-logging-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/92b0f5dd8523ebdf19edb4a977a8030a)



## おおまかな手順

おおまか手順を下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-winston-logging
cd nodejs-winston-logging
npm init -y
npm install --save dotenv winston
touch touch .env logging.js main.js
```



## コーディング

### .env

エディタで.envを開いて下記3点を入力します。

- LOG_CONSOLE: コンソールにログを出力するかどうか
- LOG_DIRNAME: ログの出力ディレクトリ
- LOG_ROTATION: ログファイルをローテーションするかどうか

例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/92b0f5dd8523ebdf19edb4a977a8030a?file=.env.example)

### logging.js

エディタでlogging.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/92b0f5dd8523ebdf19edb4a977a8030a?file=logging.js)

ポイントを下記に示します。

1. makeLoggerはwinstonのロガーを作成する関数です。第1引数はerrorやinfoなどのログレベル、第2引数はJSONやraw(生データ)などの出力形式、第3引数は出力先のログファイル名です。
2. makeFormatはwinstonのログフォーマットを作成する関数です。第1引数によってログフォーマットを作成するmakeFormatJson関数やmakeFormatRaw関数を呼び出します。
3. makeFormatJsonはJSON形式のログフォーマットを作成する関数です。winston.format.timestamp関数を使ってログにタイムスタンプを加えることができます。
4. makeFormatRawはメッセージをそのまま出力するログフォーマットを作成する関数です。
5. makeTransportFileはログをファイルへ出力するためのトランスポート（書き込み先）を作成する関数です。
6. makeTransportConsoleはログをコンソールへ出力するためのトランスポートを作成する関数です。

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/92b0f5dd8523ebdf19edb4a977a8030a?file=main.js)

ポイントを下記に示します。

1. winston.loggers.add関数を呼び出してロガーを登録します。
2. winston.loggers.get関数を呼び出して登録したロガーを取得してからログレベルに応じたメソッド（errorやwarnなど）を呼び出してログを書き込みます。



## 動作確認

ターミナルで下記のコマンドを実行してログを出力します。

```shell
node -r dotenv/config main.js
```

コンソールに下記の内容が出力されることを確認します。

```
ERROR
WARN
INFO
QUERY
ACCESS
```

コマンド実行後、作業ディレクトリ内にlogディレクトリが作成され、下記のファイルが保存されていることを確認します。

- access.log
- error.log
- info.log
- query.log
- warn.log



## おわりに

[Cloud Run](https://cloud.google.com/run?hl=ja)などにWebアプリをデプロイした場合はログファイルの出力先を/var/logに指定することで後からログを確認することができます。ただし、デバッグ用のSQLクエリログなどを常に出力しておくとその分だけストレージ料金が発生するので、常に出力するのは必須のログのみに限定し、デバッグ用のログは必要な時だけ出力するように環境変数などを通じて切り替えられるようにするとベターです（この記事のサンプルコードはそうなっていなくてすいません）。

収集したログは開発時のデバッグや運用時の不具合対応などの場面でとても助けられていますが、ログを解析する技術に疎いのであまり上手に活用できていません。また、ログ収集についてももっとより良い方法があるのではないかと常に模索しています。詳しい方がいらっしゃいましたらご指導のコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！
