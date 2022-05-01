---
title: "Node.jsでExcelファイルを読み込む方法"
emoji: "📘"
type: "tech"
topics: ["nodejs"]
published: true
---



# この記事について

この記事ではNode.jsで [SheetJS](https://www.npmjs.com/package/xlsx) (npmパッケージ名はxlsx) を使ってExcelファイルを読み込む方法について紹介します。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/230875ad58c5e2cc6d0fe2b206b19939) からダウンロード可能です。



# おおまかな手順

おおまか手順を下記に示します。

1. Excelファイルの準備
2. コーディング
3. 動作確認



# Excelファイルの準備

下表の内容で動作確認用のExcelファイルを作成し、ファイル名をdata.xlsxとして保存します。シート名についてはSheet1とします。なお、Excelファイルは [data.xlsx](https://gist.github.com/tatsuyasusukida/230875ad58c5e2cc6d0fe2b206b19939/raw/data.xlsx) からもダウンロード可能です。

| Header 1 | Header 2 | Header 3 |
| ---- | ---- | ---- |
| Row 1 Col 1 | Row 1 Col 2 | Row 1 Col 3 |
| Row 2 Col 1 | Row 2 Col 2 | Row 2 Col 3 |
| Row 3 Col 1 | Row 3 Col 2 | I am 髙橋 |



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir nodejs-xlsx-read
cd nodejs-xlsx-read
npm init -y
npm install --save xlsx
touch main.js
```

## main.js

エディタでmain.jsを開いて下記の内容を入力します。

[クリックしてmain.jsへ移動](#file-main-js)

ポイントを下記に示します。

1. XLSX.readFile関数を使ってExcelファイルを読み込みます。
2. WorkbookオブジェクトのSheetsプロパティを使ってExcelシートを取得します。
3. XLSX.utils.sheet_to_json関数を使ってExcelシートをJSON配列に変換します。



# 動作確認

ターミナルで下記のコマンドを実行してJSON配列に変換できていることを確認します。

```shell
node main.js
```

実行結果を下記に示します。

```
[
  {
    "Header 1": "Row 1 Col 1",
    "Header 2": "Row 1 Col 2",
    "Header 3": "Row 1 Col 3"
  },
  {
    "Header 1": "Row 2 Col 1",
    "Header 2": "Row 2 Col 2",
    "Header 3": "Row 2 Col 3"
  },
  {
    "Header 1": "Row 3 Col 1",
    "Header 2": "Row 3 Col 2",
    "Header 3": "I am 髙橋"
  }
]
```



# おわりに

個人的にはあまりExcelは好きではないですが、アプリの管理画面でマスタデータを一括登録する機能を設ける時などは下記3点の理由からCSVの代わりにExcelファイルを使うようにしています。

- 複数のシートを一括でアップロードできる
- 管理者さんがExcelからCSVに変換する手間を省ける
- 文字コードの問題に悩まされることが少ない（Shift_JISだと髙橋さんを登録できない、不慣れな人から「ExcelでCSVテンプレートを開くと文字化けしている」と言われる）

Excel入出力は帳票出力と並んで日本の業務システム開発における回避し難い2大要件だと考えていますが、他にも忘れているものがありましたら気軽にコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！
