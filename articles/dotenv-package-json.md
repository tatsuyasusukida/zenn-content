---
title: "package.jsonのスクリプトから.envの環境変数を使う方法3選"
emoji: "🌳"
type: "tech"
topics: ["nodejs", "dotenv", "bash", "shell"]
published: true
---

## この記事について

この記事ではpackage.jsonのスクリプトから.envの環境変数を使う方法について下記3つを紹介し、それぞれのメリットとデメリットについて考えます。

- 方法1：sourceコマンドを使う
- 方法2：dotenv-cliを使う
- 方法3：cross-varも使う


## 想定するケース

.envとpackage.jsonの内容を下記に示します。

```:.env
MESSAGE="Hello World"
```

```json:package.json
{
  "scripts": {
    "echo": "echo $MESSAGE"
  }
}
```

ターミナルで実行するコマンドを下記に示します。

```shell
npm run echo
```

実行結果を下記に示します。

```

```

.envから環境変数が読み込まれていないので何も表示されません。



## 方法1：sourceコマンドを使う

1つ目の方法ではsourceコマンドを使って環境変数を読み込みます。

```json:package.json
{
  "scripts": {
    "echo": "source .env && echo $MESSAGE"
  }
}
```

実行結果を下記に示します。

```
Hello World
```

### メリット

- npmパッケージをインストールする必要がない。

### デメリット

- .envが下記のようにクオートなし書かれていると実行できない。

```:.env
MESSAGE=Hello World
```

上記は環境変数MESSAGE="Hello"の状態でWorldコマンドを実行していると解釈されます。



## 方法2：dotenv-cliを使う

2つ目の方法では[dotenv-cli](https://www.npmjs.com/package/dotenv-cli)を使って環境変数を読み込みます。

dotenv-cliをインストールするには下記のコマンドを実行します。

```shell
npm install --save-dev dotenv-cli
```

使い方を下記に示します。

```json:package.json
{
  "scripts": {
    "echo": "dotenv -- bash -c 'echo $MESSAGE'"
  }
}
```

実行結果を下記に示します。

```
Hello World
```

できれば `dotenv echo $MESSAGE` と書きたいところですが、そうするとdotenv-cliの実行前にシェルによって$MESSAGEが空文字列に置き換えられるため、期待した結果が得られません。

### メリット

- .envがクオートなしで書かれていても実行できる。

### デメリット

- dotenv-cliをインストールする必要がある。
- Windowsなどbashがない環境では利用できない。



## 方法3：cross-varも使う

3つ目の方法ではdotenv-cliと[cross-var](https://www.npmjs.com/package/cross-var)を使って環境変数を読み込みます。

dotenv-cliとcross-varをインストールするには下記のコマンドを実行します。

```shell
npm install --save-dev dotenv-cli cross-var
```

使い方を下記に示します。

```json:package.json
{
  "scripts": {
    "echo": "dotenv cross-var 'echo $MESSAGE'"
  }
}
```

実行結果を下記に示します。

```
Hello World
```

こちらもできれば `dotenv cross-var echo $MESSAGE` と書きたいところですが、そうするとdotenv-cliの実行前にシェルによって$MESSAGEが空文字列に置き換えられるため、期待した結果が得られません。

### メリット

- bashがない環境でも利用できる。
- .envがクオートなしで書かれていても実行できる。

### デメリット

- dotenv-cliとcross-varの両方をインストールする必要がある。



## 3つの方法の比較

比較表を下記に示します。

| 方法 | npm install | .envの"" | bash |
| ---- | ---- | ---- | ---- |
| 方法1 source | ◯ 不要 | × 必要 | × 必要 |
| 方法2 dotenv-cli | × 必要 | ◯ 不要 | × 必要 |
| 方法3 dotenv-cli + cross-var | × 必要 | ◯ 不要 | ◯ 不要 |



## 3つの方法の選定基準

ご参考までに私の選定基準を下記に示します。

- Linux/macOSを使っている＆.envがクオートあり → 方法1 source
- Linux/macOSを使っている＆.envがクオートなし → 方法2 dotenv-cli
- Windowsを使う必要がある → 方法3 dotenv-cli + cross-var



## 参考にしたWebページ

この記事を書くために参考にしたWebページを下記に示します。

- [How To Use .env Variables In package.json](https://www.genui.com/resources/env-variables-json)



## おわりに

mysqlやgsutilコマンドを実行する場面などでpackage.jsonのスクリプトから.envの環境変数を使いたいことは時々あります。もう少し複雑なことをやりたい場合は[zx](https://github.com/google/zx)を併用すると幸せになれるかもしれません。
