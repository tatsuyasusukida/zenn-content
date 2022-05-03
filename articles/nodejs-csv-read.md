---
title: "Node.jsでCSVファイルを読み込む方法"
emoji: "📙"
type: "tech"
topics: ["nodejs"]
published: true
---

# この記事について

この記事ではNode.jsで [csv-parse](https://www.npmjs.com/package/csv-parse) を使ってCSVファイルを読み込む方法について紹介します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/fae31e4f59b31fc6bc5cb965ad06a875)



# おおまかな手順

おおまか手順を下記に示します。

1. CSVファイルの準備
2. コーディングの準備
3. コーディング
4. 動作確認



# Excelファイルの準備

下表の内容で動作確認用のCSVファイルを作成し、ファイル名をdata.csvとして保存します。なお、CSVファイルは [data.csv](https://gist.github.com/tatsuyasusukida/fae31e4f59b31fc6bc5cb965ad06a875/raw/data.csv) からもダウンロード可能です。

```csv
Header 1,Header 2,Header 3
Row 1 Col 1,Row 1 Col 2,Row 1 Col 3
Row 2 Col 1,Row 2 Col 2,Row 2 Col 3
Row 3 Col 1,Row 3 Col 2,"He said \"Yes, I'm 髙橋.\""
```



# コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-csv-read
cd nodejs-csv-read
npm init -y
npm install --save csv-parse
touch main.js
```



# コーディング

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/fae31e4f59b31fc6bc5cb965ad06a875?file=main.js)

ポイントを下記に示します。

1. CSV解析のオプションを指定するためのオブジェクトを作成します。
2. csv-parseのparse関数にCSVデータとオプションを渡して呼び出してCSV解析を実行します。
3. canParse関数ではCSV解析前にCSVが正しい形式になっているかどうかを事前確認するためにcsv-parseのparse関数を呼び出して例外が発生しないかをチェックします。フォームでバリデーションを行う場合に便利です。



# 動作確認

ターミナルで下記のコマンドを実行して正しく読み込めていることを確認します。特に3行3列目については入念に確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
[
  [ 'Header 1', 'Header 2', 'Header 3' ],
  [ 'Row 1 Col 1', 'Row 1 Col 2', 'Row 1 Col 3' ],
  [ 'Row 2 Col 1', 'Row 2 Col 2', 'Row 2 Col 3' ],
  [ 'Row 3 Col 1', 'Row 3 Col 2', `He said "Yes, I'm 髙橋."` ]
]
```



# おわりに

アプリの管理画面で何かとお世話になる機会の多いCSVですが書き込みは比較的簡単である一方、読み込みはエスケープなどを考える必要があるのでかなり複雑になります。CSV解析器を自前で実装するのも楽しいですが時間のない時は便利なライブラリを活用して他のもっと楽しいことに時間を費やしたい所です。csv-parseのように目立たないけれど必要とされるライブラリを開発／メンテしている方は本当に凄いと尊敬します。今はGitHubのスターくらいしか差し上げられないですがお金持ちになったらスポンサーしたいと思います。この記事をお読みの方で同じようなオープンソースをご存知の方がいましたらお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！



# 関連記事

- [Node.jsでExcelファイルを読み込む方法](https://zenn.dev/tatsuyasusukida/articles/nodejs-xlsx-read)
