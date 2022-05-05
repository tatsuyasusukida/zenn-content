---
title: "Electronで標準入出力を使ってC言語プログラムを呼び出す方法【動画版あり】"
emoji: "⚡️"
type: "tech"
topics: ["nodejs", "electron"]
published: true
---

# この記事について

この記事では標準入出力を使ってC言語プログラムを呼び出す方法を紹介します。C言語プログラムを呼び出すにあたり、Node.jsの標準ライブラリであるchild_processに含まれるspawn関数をmainプロセスから利用します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/f502ba76cffa21610e109ba11e729804)
- [動画版](https://www.youtube.com/watch?v=GvT5inJGtIE)

https://www.youtube.com/watch?v=GvT5inJGtIE



# おおまかな手順

おおまか手順を下記に示します。

1. C言語側のコーディング準備
2. C言語側のコーディング
3. C言語側のビルド
4. Electron側のコーディング準備
5. Electron側のコーディング
6. 動作確認



# C言語側のコーディング準備

ターミナルで下記のコマンドを実行してC言語側のコーディングの準備をします。

```shell
mkdir electron-child-process
cd electron-child-process
touch add.c
```



# C言語側のコーディング

## add.c

エディタでadd.cを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/f502ba76cffa21610e109ba11e729804?file=add.c)



# C言語側のビルド

下記のコマンドを実行してC言語側のソースコードをビルドします。

```shell
gcc -o add add.c
```



# Electron側のコーディング準備

ターミナルで下記のコマンドを実行してElectron側のコーディングの準備をします。

```shell
npm init -y
npm install --save-dev electron
touch index.html main.js
```


# Electron側のコーディング

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/f502ba76cffa21610e109ba11e729804?file=index.html)

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/f502ba76cffa21610e109ba11e729804?file=main.js)

ポイントを下記に示します。

1. 子プロセスの終了を待機する必要があるのでspawn関数に関係する処理全体をPromiseで囲みます。
2. spawn関数を呼び出して子プロセスを起動します。入力はコマンドライン引数argsとして渡すのが簡単です。
3. 子プロセスの標準エラー出力にエラーメッセージなどが書き込まれたことを見えるようにするためmainプロセスの標準エラー出力にパイプします。
4. 子プロセスの標準出力のdataイベントにイベントハンドラを登録して出力を取得します。
5. 子プロセスのexitイベントにイベントハンドラを登録して終了を待機します。終了すると終了コードを引数としてイベントハンドラが呼び出されるので、終了コードが0であることをチェックしてからPromiseを解決します。
6. 子プロセスのerrorイベントにイベントハンドラを登録して起動エラーや実行エラーが発生した時にPromiseをrejectします。

## package.json

エディタでpackage.jsonを開いてmainプロパティを下記の通り変更します。

```
  "main": "main.js",
```



# 動作確認

ターミナルで下記のコマンドを実行してコンソールに{"sum":"3"}と出力されることを確認します。

```shell
npx electron .
```

実行結果を下記に示します。

```
{"sum":"3"}
```



# おわりに

よく知らないのですがC言語でNode.jsネイティブモジュール的なものを作ってElectronから呼び出せるようにするのは大変そうなので、お手軽な標準入出力を使った方法を使っています。この方法のメリットとしてはC言語のプログラムを単体で起動できるのでデバッグが容易な点やビルド後にC言語プログラムの差し替えが可能な点などが挙げられます。一方、デメリットとしては子プロセスを起動するのにオーバーヘッドが生じて必要とする性能が得られなったり、過度な負荷がかかったりする恐れがあるので1秒間に何千何万回と呼び出すような場合は頑張って勉強してネイティブモジュール的なものを作った方が良いかも知れません。

用途としてはC言語でコマンドラインのプログラムを作ったけれど設定の保存など機能やGUIを作るのは大変なのでElectronで作る、みたいなユースケースが考えられます。また、C言語とElectronでソースコードの作成者が違うなどの場合も入出力のインタフェースだけ規定しておけば同じソースコードを編集する必要がないのでお互いに気兼ねなくプログラミングに集中できそうです。その際は関連記事で紹介しているElectronでfetchを使ってmainプロセスと通信する方法が役に立つかも知れません。

全然関係ないのですが、この記事を書く前に「Electronでserialportなどのネイティブモジュールを使う方法」というテーマで記事を書こうと思ったのですが [electron-rebuild](https://www.npmjs.com/package/electron-rebuild) とか使わなくても普通にネイティブモジュールが動いてしまったので急遽今回のテーマに変更した次第です。以前はElectronのmainプロセスでserialportをインポートしようとするとエラーが発生したように記憶しているのですが... 詳しい方がいましたらご指導のコメントをいただければ幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [Electronでfetchを使ってmainプロセスと通信する方法](https://zenn.dev/tatsuyasusukida/articles/electron-fetch)
