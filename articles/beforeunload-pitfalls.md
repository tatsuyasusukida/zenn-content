---
title: "[WIP] Next.js で beforeunload を使う際の落とし穴"
emoji: "🕳"
type: "tech"
topics: ["javascript", "typescript", "nextjs", "react"]
published: false
publication_name: "collabostyle"
---

## この記事について

フォーム入力のある Web アプリでフォーム内容の変更後、ページ移動やタブを閉じる前に確認ダイアログを表示したいことがあります。このような場合には window の beforeunload イベントを利用できますが、Next.js アプリで実装しようとしたところ意外と落とし穴が多くて苦労しました。この記事では実装の過程で得られた知見をまとめようと思います。

## beforeunload イベント

MDN の説明がわかりやすいので引用します。

https://developer.mozilla.org/ja/docs/Web/API/Window/beforeunload_event

> beforeunload イベントは、ウィンドウ、文書、およびそのリソースがアンロードされる直前に発生します。

> このイベントによって、ウェブページがダイアログボックスを表示し、ユーザーにページを終了するかどうかの確認が求めることができます。

確認ダイアログを表示するためのコード例を下記に示します。

```js:コード例
window.addEventListener('beforeunload', (event) => {
  event.preventDefault();
  return (event.returnValue = '');
});
```

ちなみに上記のコード例は英語版ページの方に書いてあるものです。

https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event

## Next.js での実装方法

App Router を使用して実装する場合のコード例を下記に示します。

```tsx:src/app/page.tsx
"use client";

import { useEffect } from "react";

export default function Home() {
  useEffect(() => {
    const listener = (event: BeforeUnloadEvent) => {
      event.preventDefault();
      return (event.returnValue = "");
    };

    window.addEventListener("beforeunload", listener);

    return () => {
      window.removeEventListener("beforeunload", listener);
    };
  }, []);

  return (
    <main className="container mx-auto">
      <h1 className="mt-4 mb-4 text-2xl">
        Next.js で beforeunload を使う際の落とし穴
      </h1>
      <p className="mb-4">
        このページではページ移動やタブを閉じる時に確認ダイアログが表示されます。
      </p>
    </main>
  );
}
```

## 落とし穴

僕がハマった落とし穴は下記の 3 つです。

- クリックかキータイプが必要
- event.returnValue = '' が必要
- useRef() が必要

以下、それぞれについて説明していきます。

### クリックかキータイプが必要

確認ダイアログが表示されるためにはページ内で少なくとも 1 回はクリックかキータイプが必要なようです。このことを知らなかったのでリロードボタンを連打して「なんで確認ダイアログが表示されないんだろう？」と悩んでしまいました。

普段は Mac の Google Chrome を使っていますが、Firefox と Safari でも試してみたところ同じ結果が得られたので多くのブラウザで共通の仕様なのかも知れません。

### event.returnValue = '' が必要

beforeunload に関する MDN のページには下記の記載があります。

> しかし、すべてのブラウザーがこのメソッドに対応しているわけではなく、一部はイベントハンドラーに古い方法二つのうちの一つを実装するよう求めていることに注意してください。
>
> - イベントの returnValue プロパティに文字列を代入する
> - イベントハンドラーから文字列を返す

このように書いてあるので `event.returnValue = ""` の代わりに `return ""` でも良いのだろうと思って置き換えたところ Google Chrome で確認ダイアログが表示されなくなりました。

### useRef() が必要

### メモ

```sh:コマンド
npx create-next-app \
  --typescript \
  --tailwind \
  --eslint \
  --app \
  --src-dir \
  --import-alias "@/\*" \
  --use-npm \
  beforeunload-pitfalls
cd beforeunload-pitfalls
```
