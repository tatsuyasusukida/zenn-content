---
title: "React Hook Formを使わずにフォームを作成する方法"
emoji: "📝"
type: "tech"
topics: ["javascript", "typescript", "react", "reacthookform", "nextjs"]
published: true
---

## この記事について

この記事では[React Hook Form](https://react-hook-form.com/)を使わずにフォームのバリデーションなどを行う方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/react-non-hook-form)



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
npx create-next-app --typescript react-non-hook-form
cd react-non-hook-form
mkdir lib
touch lib/validate.ts pages/form.tsx
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### lib/validate.ts

@[gist](https://gist.github.com/tatsuyasusukida/a2928bb18388ee9c3327c3e59c8d6214?file=validate.ts)

### pages/form.tsx

@[gist](https://gist.github.com/tatsuyasusukida/a2928bb18388ee9c3327c3e59c8d6214?file=form.tsx)



## 動作確認

ターミナルで下記のコマンドを実行してサーバーを起動します。

```shell
npm run dev
```

ブラウザで http://localhost:3000/form にアクセスします。

Submitボタンをクリックしてバリデーションのエラーメッセージが表示されることを確認します。

### 参考画像

![](/images/articles/react-non-hook-form/img-check-01.png)

![](/images/articles/react-non-hook-form/img-check-02.png)



## おわりに

React Hook Formを使えばほとんどのユースケースに対応できると思いますが、そうではない場合に出会った時にはこの記事を思い出して自前で実装しようと思います。
