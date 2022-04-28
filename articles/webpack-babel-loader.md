---
title: "webpackでBabelを使ってモダンなJavaScriptコードをレガシーブラウザで動かす方法"
emoji: "💝"
type: "tech"
topics: ["javascript", "webpack"]
published: true
---



# この記事について

この記事ではwebpackでBabelを使ってモダンなJavaScript（ECMASCript 8以上）コードをECMAScript 5コードに変換し、レガシーブラウザでも動かせるようにする方法について紹介します。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/6943f38b697018b274e2f88ae9037303) からダウンロード可能です。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir webpack-babel-loader
cd webpack-babel-loader
npm init -y
npm install core-js regenerator-runtime
npm install --save-dev @babel/core @babel/preset-env babel-loader webpack webpack-cli webpack-dev-server
touch app.js index.html webpack.config.js
```

## webpack.config.js

エディタでwebpack.config.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/6943f38b697018b274e2f88ae9037303?file=webpack.config.js)

ポイントを下記に示します。

1. async/awaitなどを利用するために必要なcore-jsやregenerator-runtimeなどのポリフィルライブラリをentry.polyfillに指定しています。
2. jsファイルを読み込む時にbabel-loaderを読み込むように指定しています。

## app.js

エディタでapp.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/6943f38b697018b274e2f88ae9037303?file=app.js)

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/6943f38b697018b274e2f88ae9037303?file=index.html)



# 動作確認

ターミナルで下記のコマンドを実行してwebpack-dev-serverを起動します。

```shell
webpack serve
```

ブラウザで http://localhost:8080/ にアクセスして「async関数を利用することができます。」とメッセージが表示されることを確認します。

![ブラウザで http://localhost:8080/ にアクセスした様子です。Webページの見出しはwebpackでBabelを使ってモダンなJavaScriptコードをレガシーブラウザで動かす方法であり、本文はasync関数を利用することができますとなっています。](/images/articles/webpack-babel-loader/check-01.png)

続いてブラウザで http://localhost:8080/js/app.js にアクセスしてapp.jsの内容が下記のように変換されていることを確認します。

```js:js/app.js
/******/ (function() { // webpackBootstrap
var __webpack_exports__ = {};
/*!****************!*\
  !*** ./app.js ***!
  \****************/
function asyncGeneratorStep(gen, resolve, reject, _next, _throw, key, arg) { try { var info = gen[key](arg); var value = info.value; } catch (error) { reject(error); return; } if (info.done) { resolve(value); } else { Promise.resolve(value).then(_next, _throw); } }

function _asyncToGenerator(fn) { return function () { var self = this, args = arguments; return new Promise(function (resolve, reject) { var gen = fn.apply(self, args); function _next(value) { asyncGeneratorStep(gen, resolve, reject, _next, _throw, "next", value); } function _throw(err) { asyncGeneratorStep(gen, resolve, reject, _next, _throw, "throw", err); } _next(undefined); }); }; }

function main() {
  return _main.apply(this, arguments);
}

function _main() {
  _main = _asyncToGenerator( /*#__PURE__*/regeneratorRuntime.mark(function _callee() {
    var p;
    return regeneratorRuntime.wrap(function _callee$(_context) {
      while (1) {
        switch (_context.prev = _context.next) {
          case 0:
            _context.prev = 0;
            _context.next = 3;
            return new Promise(function (resolve, reject) {
              setTimeout(function () {
                return resolve();
              }, 100);
            });

          case 3:
            p = document.createElement('p');
            p.innerHTML = 'async関数を利用することができます。';
            document.body.appendChild(p);
            _context.next = 11;
            break;

          case 8:
            _context.prev = 8;
            _context.t0 = _context["catch"](0);
            console.error(_context.t0);

          case 11:
          case "end":
            return _context.stop();
        }
      }
    }, _callee, null, [[0, 8]]);
  }));
  return _main.apply(this, arguments);
}

main();
/******/ })()
;
```



# 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [Babel loader for webpack - GitHub](https://github.com/babel/babel-loader)
- [@babel/polyfill · Babel](https://babeljs.io/docs/en/babel-polyfill/)



# おわりに

当然のことですがBabelを使うとwebpackが出力するファイルのサイズが増大し、Webページの読み込みの遅延や実行速度の低下につながるので、Babelを使う必要がないのでは使わない方がベターです。[MicrosoftのWindows Blogsの記事](https://blogs.windows.com/windowsexperience/2021/05/19/the-future-of-internet-explorer-on-windows-10-is-in-microsoft-edge/) によると2022年6月15日をもってIE11はリタイアする予定なので今後は利用しないことも増えてくるかもしれません。IEを開発者の方々、メンテナの方々、IE対応に苦労したWeb制作関係の方々、皆さん大変お疲れさまでした。最後までお読みいただきありがとうございました！



# 関連記事

- [webpackでDevtoolやライブリロードなどをすべて無効にする方法](https://zenn.dev/tatsuyasusukida/articles/webpack-devtool-disable)
- [webpackで遅延読み込みを使って大きなファイルを小分けにする方法](https://zenn.dev/tatsuyasusukida/articles/webpack-devtool-disable)
- [webpackでVueのテンプレートエンジンとしてPugを使う方法](https://zenn.dev/tatsuyasusukida/articles/webpack-vue-pug)
