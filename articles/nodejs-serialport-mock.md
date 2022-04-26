---
title: "Node SerialPortでテスト用のモックを作成する方法"
emoji: "🥣"
type: "tech"
topics: ["nodejs", "serialport"]
published: true
---



# この記事について

この記事では [Node SerialPort](https://www.npmjs.com/package/serialport) でテスト用のモックを作成する方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-serialport-mock
cd nodejs-serialport-mock
npm init -y
npm install --save serialport
touch main.js
```

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/9e59cc195b774cddfed6831b3a89d7df?file=main.js)

ポイントを下記に示します。

1. /dev/ROBOTという名前のテスト用のシリアルポートを作成しています。
2. シリアルポートのモック（テストのために指定した通りに振る舞う）クラスを作成しています。
3. PINGと送信した時にPONGと受信するようにしています。
4. 環境変数SERIAL_PORT_MOCKによってモックの使用を切り替えています。実際のアプリケーションでは `process.env.SERIAL_PORT_MOCK === '1'` としてSerialPortの使用をデフォルトにします。
5. シリアルポートを作成しています。
6. シリアルポートをオープンしています。
7. データを受信した時にPromiseに受信データを送信しています。
8. エラーが発生した時にPromiseのエラーを通知しています。
9. シリアルポートにPINGと送信しています。
10. 受信したデータを文字列にして出力しています。
11. シリアルポートを閉じています。
12. エラー発生時もシリアルポートを閉じています。finallyを使っても良いですが過去に痛い目にあったので重複して書いています。



# 動作確認

下記のコマンドを実行してPINGを送信するとPONGを受信することを確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
send: PING
receive: PONG
```



# おわりに

シリアル通信を扱うアプリケーションでは実機、シミュレーター、ループバックによるテストも有効ですが、モックを使うことですぐにフィードバックを得られるようになりバグの早期発見につながります。この記事がマイコンへのプログラム書き込みを何回も繰り返して疲れた方への救いの手となれば幸いです。最後まで読んでいただきありがとうございました！



# 関連記事

- [Node.jsでシリアル通信などの待ち行列を実装する方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-serialport-queue)
