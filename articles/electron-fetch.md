---
title: "Electronでfetchを使ってmainプロセスと通信する方法"
emoji: "☎️"
type: "tech"
topics: ["nodejs", "electron"]
published: true
---

# この記事について

この記事ではElectronでrendererプロセスから [Fetch API](https://developer.mozilla.org/ja/docs/Web/API/Fetch_API) を使ってmainプロセスと通信する方法について紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/43f9c962297db5991bc4511f351e0eb0)



# おおまかな手順

おおまか手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir electron-fetch
cd electron-fetch
npm init -y
npm install --save express
npm install --save-dev electron
touch index.html main.js renderer.js
```

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/43f9c962297db5991bc4511f351e0eb0?file=main.js)

ポイントを下記に示します。

1. createWindow関数内でExpressを使ってWebサーバーを作成します。
2. /へのGETリクエストに対してindex.htmlのファイル内容を返送します。
3. /render.jsへのGETリクエストに対してrender.jsのファイル内容を返送します。
4. fetchを使って通信するためのAPIを設けます（このサンプルではGET /api/status）。
5. router.listenメソッドを呼び出してWebサーバーを起動します。
6. mainWindow.loadURLメソッドを呼び出してWebサーバーにアクセスします。

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/43f9c962297db5991bc4511f351e0eb0?file=index.html)

## renderer.js

エディタでrenderer.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/43f9c962297db5991bc4511f351e0eb0?file=renderer.js)

## package.json

エディタでpackage.jsonを開いてmainプロパティを下記の通り変更します。

```
  "main": "main.js",
```



# 動作確認

ターミナルで下記のコマンドを実行してコンソールに{"ok":true}と出力されることを確認します。

```shell
npx electron .
```

![上記のコマンドを実行してElectronのウィンドウが起動した様子です。コンソールに{"ok":true}と出力されています](/images/articles/electron-fetch/img-check-01.png)



# おわりに

Electronではプロセス間通信(IPC)のAPIを用意されているのでrendererプロセスからmainプロセスに通信するにあたってわざわざfetchを使う必要はありません。しかしながら、通信モデルがメッセージパッシングなのでリクエスト／レスポンス型の通信の場合はawaitするためにPromiseを書かなければならず、また、複数のリクエストを同時に送信する場合はユニークなIDなどを発行してリクエストを特定できるようにする必要があります。これに対してfetchの場合はawaitするためにPromiseを書かなくてよく、また、リクエストとレスポンスの対応を自動で管理してくれるのでとても楽です。デメリットとしてはTCPポートを一つオープンする必要があり、また、このTCPポートには同じPCからアクセスできてしまうのでセキュリティ上の懸念があります。

IPCとfetchの使い分けですが、同じ目的のために複数の手段があるとコーディングの時に迷いが生じるので私は次のようにルールを決めています。

- 原則としてfetchを使う
- メニューなどのElectron固有の機能を使う場合にはIPCを使う
- mainプロセスからrendererプロセスに通信する場合はIPCを使う

Electronを使ったWindows向けのデスクトップアプリをバージョンアップの時にC♯に移植する仕事をしましたがXAMLなどに苦しめられた苦い思い出があります。どうにかして完成にまで漕ぎ着けましたがバグだらけで使いものにならなかったので結局Electronに戻して満足のいくものを作り直しました。Web技術を活用してデスクトップアプリを開発できるElectronは本当に素晴らしい思います（C♯も言語自体は素晴らしいと思うのですが...）。

この記事をお読みの方の中でWindowsアプリ開発に苦労した経験をお持ちの方やコツなどをご存知の方はお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！
