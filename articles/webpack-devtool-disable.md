---
title: "webpackでDevtoolやライブリロードなどをすべて無効にする方法"
emoji: "🎁"
type: "tech"
topics: ["javascript", "webpack"]
published: true
---



# この記事について

この記事では [webpack](https://webpack.js.org/) でDevtoolやライブリロードなどをすべて無効にするwebpack.config.jsの書き方について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認

ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/7851f96a2bcffcfd0a9c8cbeeee8b640) からダウンロード可能です。



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir webpack-devtool-disable
cd webpack-devtool-disable
npm init -y
npm install --save-dev webpack webpack-cli webpack-dev-server
touch app.js webpack.config.js
```

## app.js

エディタでapp.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/7851f96a2bcffcfd0a9c8cbeeee8b640?file=app.js)

## webpack.config.js

エディタでwebpack.config.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/7851f96a2bcffcfd0a9c8cbeeee8b640?file=webpack.config.js)

ポイントを下記に示します。

1. `devtool: false` を指定してDevtoolを無効にしています。
2. `devServer.client: false` を指定して（websocketなどを管理していると思われる）クライアントを無効にしています。
3. `devServer.hot: false` を指定してホットモジュールリプレースメントを無効にしています。
4. `devServer.liveReload: false` については `devServer.hot: false` によって無効になるので指定を省略しています。



# 動作確認

ターミナルで下記のコマンドを実行してwebpack-dev-serverを起動します。

```shell
webpack serve
```

ブラウザで http://localhost:8080/js/app.js にアクセスして下記の内容が出力されることを確認します。

```js:js/app.js
/******/ (() => { // webpackBootstrap
var __webpack_exports__ = {};
/*!****************!*\
  !*** ./app.js ***!
  \****************/
console.log('This is app.js')
/******/ })()
;
```



# おわりに

webpackのDevtoolやライブリロードは便利ですがContent Security Policyなどによるコンソールのエラー表示が煩わしく感じるので基本的には無効にしています。便利な機能が使えないのでかなり損しているかも知れないのでwebpackの超便利な機能などをご存知の方がいらっしゃいましたらお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました。



# 関連記事

- [webpackで遅延読み込みを使って大きなファイルを小分けにする方法](https://zenn.dev/tatsuyasusukida/articles/webpack-devtool-disable)
- [webpackでVueのテンプレートエンジンとしてPugを使う方法](https://zenn.dev/tatsuyasusukida/articles/webpack-vue-pug)