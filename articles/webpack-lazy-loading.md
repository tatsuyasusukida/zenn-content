---
title: "webpackで遅延読み込みを使って大きなファイルを小分けにする方法"
emoji: "🔪"
type: "tech"
topics: ["javascript", "webpack"]
published: true
---



# この記事について

この記事では [webpack](https://webpack.js.org/) の遅延読み込みを使って大きなファイルを小分けにして実行時に動的に読み込む方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認

ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/cb8935596a5e3b8b84d988f7ae25ee21) からダウンロード可能です。



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir webpack-devtool-disable
cd webpack-devtool-disable
npm init -y
npm install --save-dev webpack webpack-cli webpack-dev-server
touch app.js page1.js page2.js webpack.config.js
```

## app.js

エディタでapp.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/cb8935596a5e3b8b84d988f7ae25ee21?file=app.js)

ポイントを下記に示します。

1. URLのpathnameがpage1である時にimport関数を使って./page1.jsを読み込んでいます。import関数の呼び出しのカッコ内にwebpackChunkNameを含むコメントを書くことでビルド時に出力されるファイル名を指定することができます。
2. URLのpathnameがpage2である時にimport関数を使って./page2.jsを読み込んでいます。

## page1.js

エディタでpage1.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/cb8935596a5e3b8b84d988f7ae25ee21?file=page1.js)

## page2.js

エディタでpage2.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/cb8935596a5e3b8b84d988f7ae25ee21?file=page2.js)

## webpack.config.js

エディタでwebpack.config.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/cb8935596a5e3b8b84d988f7ae25ee21?file=webpack.config.js)



# 動作確認

ターミナルで下記のコマンドを実行してwebpack-dev-serverを起動します。

```shell
webpack serve
```

ブラウザで http://localhost:8080/js/app.js にアクセスして下記の内容が含まれていることを確認します。

```js:js/app.js
/*!****************!*\
  !*** ./app.js ***!
  \****************/
async function main () {
  try {
    if (window.location.pathname === '/page1') { // <1>
      await __webpack_require__.e(/*! import() | pages/page-one */ "pages/page-one").then(__webpack_require__.t.bind(__webpack_require__, /*! ./page1.js */ "./page1.js", 23))
    } else if (window.location.pathname === '/page2') { // <2>
      await __webpack_require__.e(/*! import() | pages/page-two */ "pages/page-two").then(__webpack_require__.t.bind(__webpack_require__, /*! ./page2.js */ "./page2.js", 23))
    }
  } catch (err) {
    console.error(err)
  }
}

main()
```

続いてブラウザで http://localhost:8080/js/pages/page-one.app.js にアクセスして下記の内容と同じであることを確認します。

```js:js/pages/page-one.app.js
(self["webpackChunkwebpack_lazy_loading"] = self["webpackChunkwebpack_lazy_loading"] || []).push([["pages/page-one"],{

/***/ "./page1.js":
/*!******************!*\
  !*** ./page1.js ***!
  \******************/
/***/ (() => {

console.log('This is page 1')

/***/ })

}]);
```

続いてブラウザで http://localhost:8080/js/pages/page-two.app.js にアクセスして下記の内容と同じであることを確認します。

```js:js/pages/page-two.app.js
(self["webpackChunkwebpack_lazy_loading"] = self["webpackChunkwebpack_lazy_loading"] || []).push([["pages/page-two"],{

/***/ "./page2.js":
/*!******************!*\
  !*** ./page2.js ***!
  \******************/
/***/ (() => {

console.log('This is page 2')

/***/ })

}]);
```



# 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [Lazy Loading | webpack](https://webpack.js.org/guides/lazy-loading/)



# おわりに

WebページによってはwebpackのJavaScriptコードの大部分を必要としないことはよくあるので、そのような場合はこの記事で紹介した方法を使うことで必要としないコードの読み込みを省略して高速化を図ることができます。この記事がwebpackのJavaScriptコードが大きくなりすぎて困っている方の助けになれば幸いです。最後までお読みいただきありがとうございました。



# 関連記事

- [webpackでDevtoolやライブリロードなどをすべて無効にする方法](https://zenn.dev/tatsuyasusukida/articles/webpack-devtool-disable)
