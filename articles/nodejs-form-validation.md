---
title: "Node.jsでユーザー入力のバリデーションを行う方法"
emoji: "👍"
type: "tech"
topics: ["nodejs", "validation"]
published: false
---

## この記事について

この記事ではWebページのフォームなどからユーザーが入力したデータに対してNode.jsでバリデーションを行う方法について紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/fa28e2b0a8bb810b179556a42b946b97#file-form-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/fa28e2b0a8bb810b179556a42b946b97)
- [動画版](https://www.youtube.com/watch?v=xxxxxxxxxxx)

https://www.youtube.com/watch?v=xxxxxxxxxxx



## おおまかな手順

おおまか手順を下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-form-validation
cd nodejs-form-validation
npm init -y
npm install --save validator
touch form.js main.js validate.js
```



## コーディング

### form.js

エディタでform.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/fa28e2b0a8bb810b179556a42b946b97?file=form.js)

### validate.js

エディタでvalidate.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/fa28e2b0a8bb810b179556a42b946b97?file=validate.js)

ポイントを下記に示します。

1. バリデーション結果のオブジェクトを作成するmakeValidationCompany関数を定義します。
2. バリデーションを行うvalidateCompany関数を定義します。
3. req.body.formにアクセスできるかどうかをチェックします。validateCompany関数を呼び出す時にExpressでreqオブジェクトを渡すことを想定しているので引数をreqにしています。
4. isNotEmptyはスペースやタブ以外の文字1つ以上の正規表現でチェックしています。
5. isKanaはUnicodeでカタカナの範囲に含まれる文字1つ以上の正規表現でチェックしています。
6. isDigitsSevenは数字7文字の正規表現でチェックしています。
7. isDigitsは数字1つ以上の正規表現でチェックしています。
8. isUrlはvalidator.isURL関数を使ってチェックしています。

### main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/fa28e2b0a8bb810b179556a42b946b97?file=main.js)



## 動作確認

ターミナルで下記のコマンドを実行します。

```shell
node main.js
```

実行結果を下記に示します。

```
{
  validationFail: {
    ok: false,
    name: { ok: false, isNotEmpty: false },
    kana: { ok: false, isKana: false },
    zip: { ok: false, isDigitsSeven: false },
    address: { ok: false, isNotEmpty: false },
    tel: { ok: false, isDigits: false },
    email: { ok: false, isNotEmpty: false },
    website: { ok: false, isUrl: false }
  },
  validationSucceed: {
    ok: true,
    name: { ok: true, isNotEmpty: true },
    kana: { ok: true, isKana: true },
    zip: { ok: true, isDigitsSeven: true },
    address: { ok: true, isNotEmpty: true },
    tel: { ok: true, isDigits: true },
    email: { ok: true, isNotEmpty: true },
    website: { ok: true, isUrl: true }
  }
}
```

下記2点を確認します。

- validationFailに含まれるすべてのフィールドがfalseであること
- validationSucceedに含まれるすべてのフィールドがtrueであること



## おわりに

フォームのバリデーションは面倒ですが、アプリを使ってくれる人（ユーザー）からの入力を必要とする場合はほぼ必須な上、エラーメッセージ表示などを含むバリデーションの出来具合はユーター体験に大きな影響を及ぼすので手抜きもできません。理想をいえば下記のような機能を設けられればナイスです。

- ユーザーが入力したらほぼリアルタイムでわかりやすいエラーメッセージが表示される
- 次へ進むなどのボタンを押した時にエラーが残っている場合は該当箇所へ移動する

しかし、なかなかそこまで手が回りません。例えば、エラーメッセージを即座に表示できるようにするには、バックエンドとフロントエンドでそれぞれ別々にバリデーションを実装することが望ましいですが、両方の整合性を保つのが結構大変です。結局、バックエンドにだけバリデーションを実装してフロントエンドからはfetchなどでバックエンドのバリデーションのコードを呼び出す、みたいにして妥協することになります。

この記事をお読みの皆さんはどのようにバリデーションを実装されているでしょうか？ご意見ご感想などございましたらお気軽にコメントをいただければ幸いです。その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！
