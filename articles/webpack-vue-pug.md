---
title: "webpackでVueのテンプレートエンジンとしてPugを使う方法"
emoji: "🐶"
type: "tech"
topics: ["javascript", "webpack"]
published: true
---



# この記事について

この記事では [webpack](https://webpack.js.org/) で [Vue Loader](https://vue-loader.vuejs.org/) を使って [Vue](https://vuejs.org/) のシングルファイルコンポーネントを作成する時にテンプレートエンジンとして [Pug](https://pugjs.org/) を使う方法について紹介します。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/3a05ca2aa34ead3dc3c54ce5f658daca) からダウンロード可能です。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認





# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir webpack-vue-pug
cd webpack-vue-pug
npm init -y
npm install --save vue
npm install --save-dev pug pug-plain-loader vue-loader vue-template-compiler webpack webpack-cli webpack-dev-server
touch app.js component.vue index.html webpack.config.js
```

## webpack.config.js

エディタでwebpack.config.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/3a05ca2aa34ead3dc3c54ce5f658daca?file=webpack.config.js)

ポイントを下記に示します。

1. Vueファイルのtemplate要素でlang属性にpugが指定された時にローダーとしてpug-plain-loaderを使うように設定しています。
2. Vueファイルのローダーとしてvue-loaderを使うように設定しています。

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/3a05ca2aa34ead3dc3c54ce5f658daca?file=index.html)

## app.js

エディタでapp.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/3a05ca2aa34ead3dc3c54ce5f658daca?file=app.js)

## component.vue

エディタでcomponent.vueを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/3a05ca2aa34ead3dc3c54ce5f658daca?file=component.vue)

ポイントを下記に示します。

1. template要素のlang属性をpugに設定することでテンプレートエンジンとしてPugを使用することを指定しています。



# 動作確認

ターミナルで下記のコマンドを実行してwebpack-dev-serverを起動します。

```shell
webpack serve
```

ブラウザで http://localhost:8080/ にアクセスして「Hello world!」と表示されることを確認します。

![ブラウザで http://localhost:8080/ アクセスした様子です。Webページの見出しはwebpackでPugを使ってVueのテンプレートを書く方法であり、本文はHello world!です。](/images/articles/webpack-vue-pug/check-01.png)



# 参考にしたWebページ

この記事を作成するために参考にしたWebページを下記に示します。

- [Introduction | Vue Loader](https://vue-loader.vuejs.org/)



# おわりに

私はWebアプリを開発する時にExpressでPugを使ってモックアップを作り、仕様が確定してからVueを使ってユーザーインタフェースを実装していくことが多いので、ExpressからVueへPugのソースコードをコピーできることにとても助けられています。

しかしながら「vue pug」でGoogle検索するとその未来は明るくないようです。私と同じようにVueファイルのテンプレートエンジンとしてPugを使っていた方がHTMLへ回帰している様子が散見されます。大きな理由としてはlinter（ソースコードの書き方について警告を表示するツール）などの静的解析ツールが利用できないことに起因しているようです。

HTMLのソースコードを読むのはともかくとして書くのはEmmetなどを使えばPugなしでもやっていけるので、今後はExpressでも [EJS](https://ejs.co/) を使用するなどして徐々にPugからHTMLへ移行していこうと感じました。

この記事をお読みになった方の中で新しいツールや言語などを試したけれど最終的には元に戻した経験などがありましたらお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [webpackでDevtoolやライブリロードなどをすべて無効にする方法](https://zenn.dev/tatsuyasusukida/articles/webpack-devtool-disable)
- [webpackで遅延読み込みを使って大きなファイルを小分けにする方法](https://zenn.dev/tatsuyasusukida/articles/webpack-devtool-disable)

