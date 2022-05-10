---
title: "Node.jsでPuppeteerを使ってWebアプリのUIテストを自動化する方法【動画版あり】"
emoji: "🎎"
type: "tech"
topics: ["nodejs", "puppeteer"]
published: true
---

## この記事について

この記事ではNode.jsからGoogle ChromeをコントロールするためのAPIを提供する [Puppetter](https://pptr.dev/) を使ってWebアプリのUIテスト（E2Eテスト）を自動化する方法について紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/48676c28011264b0a3a4e73b5f4ba6c6)
- [動作確認用デモ](https://gist.githack.com/tatsuyasusukida/48676c28011264b0a3a4e73b5f4ba6c6/raw/index.html)
- [動画版](https://www.youtube.com/watch?v=mzy9JFM5I8Y)

https://www.youtube.com/watch?v=mzy9JFM5I8Y



## おおまかな手順

おおまか手順を下記に示します。

1. 動作確認用デモの作成準備
2. 動作確認用デモの作成
3. テストコードの作成準備
4. テストコードの作成
5. 動作確認



## 動作確認用デモの作成準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-test-e2e-puppeteer
cd nodejs-test-e2e-puppeteer
npm init -y
npm install --save http-server puppeteer
touch index.html main.js
```



## 動作確認用デモの作成

### index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/48676c28011264b0a3a4e73b5f4ba6c6?file=index.html)

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/48676c28011264b0a3a4e73b5f4ba6c6?file=main.js)



## テストコードの作成準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
touch test.js
```



## テストコードの作成

### test.js

エディタでtest.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/48676c28011264b0a3a4e73b5f4ba6c6?file=test.js)

ポイントを下記に示します。

1. Puppeteerのブラウザを起動します。起動オプションにヘッドレスを無効にするオプションを指定することでブラウザを見えるようにすることができます。
2. browser.newPageメソッドを呼び出して新規にページ（タブ）を生成します。
3. page.gotoメソッドを呼び出してテスト対象のWebページのURLにアクセスします。同時にpage.waitForNavigationメソッドなどwait系メソッドを呼び出して完了を待機します。
4. page.typeやpage.clickメソッドを使ってinputやbutton要素にキーボード入力やクリックをします。
5. page.$evalメソッドを使ってWebページ内の要素のinnerHTMLなどを取得します。
6. Node.jsの標準ライブラリのassert.strictEqualを使って実際の値と期待する値が一致するかチェックします。



## 動作確認

ターミナルで下記のコマンドを実行して動作確認用デモを起動します。

```shell
npx http-server -c-1
```

続いて新規のターミナルを起動してから下記のコマンドを実行してテストを実行します。

```shell
node test.js
```

コンソールに下記の実行結果が出力されることを確認します。

```
{ actual: '3', expected: '3' }
```



## おわりに

WebアプリでUIテスト自動化といえば [Selenium](https://www.npmjs.com/package/selenium-webdriver) ですがPuppeteerと比べるとハマりやすいポイントが多いように感じたのでお手軽なPuppeteerを使っています。Pupppeteerはヘッドレスなので実行してもブラウザが表示されませんが、この記事で紹介したように起動オプションでヘッドレスを無効にすることを指定することでブラウザを見えるようにすることも可能です。ブラウザを見えるようにすることでテストがうまくいかなかった時に原因を探ったり、一般の方に自動テストを実施している様子を見せてドヤれたりするなど様々なメリットがあります。自動テスト程度でドヤってたらダメですね、すいません。

UIテストではWebページ内の要素にアクセスする必要がありますが、この際にWebページのアクセシビリティが高いと要素へのアクセスが簡単になるのでテストコードの作成が容易になるように思います。UIテストがしやすい＝アクセシビリティが高いではないですが一定の正の相関関係はあると思うので [WAI-ARIA](https://www.w3.org/TR/wai-aria/) を使いこなせるように頑張って勉強しようと思います。

あまり関係ないですがPuppeteerはPDFを出力する機能もあるのでHTMLを入力するとPDFが出力されるマイクロサービスを作ってCloud Runにデプロイしたいと考えていますが、フォントとかが面倒な気がして最後まで辿り着けなくて自信を喪失しそうで心配でなかなか着手できていません（Webフォントを使えば良いのかしら）。

私はWebアプリの開発で単体テスト／APIテスト／UIテストの3つを基本にしていますが、UIテストは他の2つのテストに比べてテストコードの作成や実行に時間がかかってしまうのでどうしてもおろそかになってしまう傾向があります。PuppeteerのようにUIテストを手助けするツールなどをご存知の方がいましたらご指導のコメントをいただければ幸いです、もちろんその他のコメントも大歓迎です。最後までお読みいただきありがとうございました！



## 関連記事

- [Node.jsで標準ライブラリだけを使って単体テストを行う方法](https://gist.github.com/tatsuyasusukida/4e90436db3bf48ab6e840c098c701ea3)
- [Node.jsでfetchを使ってAPIテストを行う方法](https://gist.github.com/tatsuyasusukida/c21445d4a36b07013d79b2baa54e6d61)
