---
title: "Next.jsでBootstrapのドロップダウンを使う方法"
emoji: "💧"
type: "tech"
topics: ["nextjs", "bootstrap"]
published: true
---

## この記事について

この記事ではNext.jsでBootstrap 5の[ドロップダウン](https://getbootstrap.com/docs/5.2/components/dropdowns/#content)の表示と非表示を切り替える方法について紹介します。関連リソースを下記に示します。

- [ソースコード](https://github.com/tatsuyasusukida/nextjs-bootstrap-dropdown)



## おおまかな流れ

おおまかな流れを下記に示します。

1. コーディングの準備
2. コーディング
3. 動作確認



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
npx create-next-app nextjs-bootstrap-dropdown --typescript
cd nextjs-bootstrap-dropdown
npm install --save bootstrap
npm run dev
```



## コーディング

### pages/\_app.tsx

エディタでpages/\_app.tsxを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/0b5ffc565ad80102fe9f40dca97f6780?file=_app.tsx)

### pages/index.tsx

エディタでpages/index.tsxを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/0b5ffc565ad80102fe9f40dca97f6780?file=index.tsx)

ポイントを下記に示します。

1. ドロップダウンの表示／非表示を表すstateを作成します。
2. ドロップダウンのトグラーがクリックされた時に表示と非表示を切り替えます。
3. ドロップダウンのメニューがクリックされた時にドロップダウンを非表示に示します。
4. ドロップダウンのメニューが表示されている状態でメニュー以外のエリアをクリックされた時にドロップダウンを非表示にします。



## 動作確認

ブラウザで http://localhost:3000/ にアクセスします。

画面右上のDropdownをクリックするとメニューが表示されることを確認します。

ドロップダウンのメニューまたはメニュー以外のエリアをクリックするとメニューが非表示になることを確認します。

### 参考画像

![](/images/articles/nextjs-bootstrap-dropdown/img-check-01.png)

![](/images/articles/nextjs-bootstrap-dropdown/img-check-02.png)



## おわりに

Next.js(React)を利用するのであれば[Chakui UI](https://chakra-ui.com/)や[Tailwind UI](https://tailwindui.com/)を利用した方が相性が良さそうなので、しっかり勉強して早めに移行できるように頑張りたいと思います。おすすめのUIライブラリがありましたらご指導のコメントをいただければ幸いです。最後までお読みいただきありがとうございました！
