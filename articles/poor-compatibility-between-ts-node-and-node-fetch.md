---
title: "ts-nodeとnode-fetchの相性が悪い"
emoji: "🔌"
type: "tech"
topics: ["typescript", "nodejs", "fetch"]
published: true
---



## この記事について

TypeScriptでfetchを使った簡単なスクリプトを書こうとしたところ[ts-node](https://www.npmjs.com/package/ts-node)と[node-fetch](https://www.npmjs.com/package/node-fetch)の相性が悪くてずいぶん苦労しました。この記事では再現手順、原因、解決方法についてまとめます。



## エラーの再現手順

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir ts-node-fetch
cd ts-node-fetch
npm init -y
npm install --save ts-node node-fetch
touch main.ts
```

エディタでmain.tsを開いて下記の内容を入力します。

```ts:main.ts
import fetch from "node-fetch";

async function main () {
  const response = await fetch('https://zenn.dev/')
  console.info(response.status)
}

main()
```

ターミナルで下記のコマンドを実行してスクリプトを起動します。

```shell
npx ts-node main.ts
```

下記のエラーメッセージが表示されることを確認します。

```
/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/index.js:851
            return old(m, filename);
                   ^
Error [ERR_REQUIRE_ESM]: require() of ES Module /Users/susukida/workspace/js/ts-node-fetch/node_modules/node-fetch/src/index.js from /Users/susukida/workspace/js/ts-node-fetch/main.ts not supported.
Instead change the require of index.js in /Users/susukida/workspace/js/ts-node-fetch/main.ts to a dynamic import() which is available in all CommonJS modules.
    at Object.require.extensions.<computed> [as .js] (/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/index.js:851:20)
    at Object.<anonymous> (/Users/susukida/workspace/js/ts-node-fetch/main.ts:6:38)
    at Module.m._compile (/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/index.js:857:29)
    at Object.require.extensions.<computed> [as .ts] (/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/index.js:859:16)
    at phase4 (/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/bin.js:466:20)
    at bootstrap (/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/bin.js:54:12)
    at main (/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/bin.js:33:12)
    at Object.<anonymous> (/Users/susukida/workspace/js/ts-node-fetch/node_modules/ts-node/dist/bin.js:579:5) {
  code: 'ERR_REQUIRE_ESM'
}
```



## エラーの原因

node-fetchはv3からESM-onlyモジュールとなっているので`require()`を使ってインポートすることができません。一方、ts-nodeはデフォルトではCommonJSを使っているので`require()`でインポートしようとします。`require()`でインポートできないnode-fetchを`require()`しようとしていることがエラーの原因です。



## エラーの解決方法

### 方法1：node-fetch@2をインストール

おすすめの方法です。node-fetchをインストールするときにv2を指定します。

```shell
npm install node-fetch@2
```

動作を確認するには下記のコマンドを実行します。

```shell
npx ts-node main.ts
```

実行結果を下記に示します。

```
200
```

node-fetchのv2はCommonJSモジュールであり`require()`でインポートできるのでエラーが発生しなくなります。

### 方法2：ESMを有効化

おすすめではない方法です。まずはpackage.jsonを編集します。

```json:package.json
{
  "type": "module"
}
```

次にtsconfig.jsonを編集します。

```json:tsconfig.json
{
  "ts-node": {
    "compilerOptions": {
      "module": "esnext",
      "moduleResolution": "node"
    }
  }
}
```

動作を確認するには下記のコマンドを実行します。`--esm `オプションを忘れずに指定します。

```shell
npx ts-node --esm main.ts
```

実行結果を下記に示します。

```
200
```

第1にpackage.jsonで`"type": "module"`を指定してNode.js側でESMを有効化します。第2にtsconfig.jsonでcompilerOptionsを指定してTypeScript側で`import`を`require()`に変換しないようにします。第3にts-nodeで起動するときに`--esm`オプションを指定します。3つのうちどれか1つでも欠けると動作しません。



## おわりに

Node.jsのv18からはデフォルトでfetchが使用できます。

[Node.js 18 is now available! | Node.js](https://nodejs.org/de/blog/announcements/v18-release-announce/)

また、2022年10月25日からはv18がLTSになります。

[Releases | Node.js](https://nodejs.org/en/about/releases/)

2022年11月以降はts-nodeとnode-fetchの相性が悪さに悩まされることは無くなりそうです。
