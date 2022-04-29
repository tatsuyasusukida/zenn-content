---
title: "Node.jsで浮動小数点数の誤差に対処する方法"
emoji: "🔢"
type: "tech"
topics: ["nodejs"]
published: true
---



# この記事について

この記事ではNode.jsで [big.js](https://www.npmjs.com/package/big.js)を使って 浮動小数点数の誤差に対処する方法を紹介します。



# おおまかな手順

おおまか手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-big-number
cd nodejs-big-number
npm init -y
npm install --save big.js
touch main.js
```

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/4103054b89bdd010a19dcdf272f47dd6?file=main.js)

ポイントを下記に示します。

1. big.jsをBigとしてインポートしています。
2. xxに20e-6を代入しています、Bigのコンストラクタでは文字列を使います。
3. yyに20e-8を代入しています、xxと同様にBigのコンストラクタでは文字列を使います。
4. zzにxxとyyの除算結果を代入しています、除算にはdivメソッドを使い、Bigオブジェクトから数値に戻すにはtoNumberメソッドを使います。



# 動作確認

ターミナルで下記のコマンドを実行してzzが100であることを確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
{ z: 100.00000000000001, zz: 100 }
```


# おわりに

big.jsには同じ作者によって開発された [bignumber.js](https://www.npmjs.com/package/bignumber.js) と [decimal.js](https://www.npmjs.com/package/decimal.js) の2つの姉妹ライブラリがあり、それぞれ下記のような違いがあります。

- bignumber.js: 16進数も扱うことができる、RANGE, CRYPTO, MODULE_MODEなどの追加の設定できる。
- decimal.js: 精度を有効数字で指定できる（decimal.js以外は小数点以下の桁数で指定する）、すべての計算が指定した有効数字に丸められる、三角関数などが使える。

サイズとしてはbig.js < bignumber.jsであり、big.jsで間に合うのであればbig.jsを使った方が良いと思います。また、decimal.jsはbig.jsやbignumber.jsとは用途が異なるので、有効数字で精度を指定したい場合には便利だと思います。

この記事が浮動小数点数の誤差に悩んでいる方のお役に立てば幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [Node.jsで非同期処理を扱う6通りの方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-async-await)
- [Node.jsでシリアル通信などの待ち行列を実装する方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-serialport-queue)
- [Node.jsでSHA-256のハッシュ値を計算する2通りの方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-sha256-hash)
