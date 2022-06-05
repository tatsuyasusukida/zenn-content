---
title: "Node.jsでGoogle Text-to-Speech を使ってテキストを音声に変換する方法"
emoji: "🎤"
type: "tech"
topics: ["gcp", "nodejs"]
published: false
---

## はじめに

この記事ではNode.jsでGoogle Cloud Platform (GCP)のCloud Text-to-Speechを使ってテキストを音声に変換する方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/4c2b71df2e82bf1fa017407338b03166#file-main-mjs)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/4c2b71df2e82bf1fa017407338b03166)



## おおまかな手順

おおまかな手順を下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir gcp-text-to-speech
cd gcp-text-to-speech
npm init -y
npm install --save @google-cloud/text-to-speech
touch input.txt main.mjs
```



## コーディング

### main.mjs

エディタでmain.mjsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/4c2b71df2e82bf1fa017407338b03166?file=main.mjs)

### input.txt

エディタでindex.txtを開いて音声に変換したいテキストの内容を入力します。例を下記に示します。

@[gist](https://gist.github.com/tatsuyasusukida/4c2b71df2e82bf1fa017407338b03166?file=input.example.txt)



## 動作確認

下記のコマンドを実行してGCPのAPIにアクセスできるようにします。

```shell
gcloud auth application-default login
```

下記のコマンドを実行してテキストを音声に変換します。

```shell
node main.mjs
```



## おわりに

[JavaScriptで動画を撮影する方法のデモ動画](https://www.youtube.com/watch?v=GPINZB8ENUQ) を作成する時にGoogle Text-to-Speechを使って動画の中で使用する音声を生成しようとしたのですが、CLI（gcloudコマンド）やGUI（Webコンソール）から利用できないと知った時は少し驚きました。一応 [コマンドラインを使用してテキストから音声を作成する](https://cloud.google.com/text-to-speech/docs/create-audio-text-command-line) ドキュメントはあるのですが、curlコマンドを実行してAPIのエンドポイントにHTTPリクエストを送信する方法でかえって手間がかかりそうだったのでNode.jsのクライアントライブラリを使った方が手っ取り早いと思いソースコードを作成しました。その時に作成したソースコードを元にこの記事が誕生しました。
