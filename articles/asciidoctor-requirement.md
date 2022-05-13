---
title: "Asciidoctorで仕様書らしく体裁を整える方法"
emoji: "📄"
type: "tech"
topics: ["asciidoc"]
published: true
---



## この記事について

この記事では [Asciidoctor](https://asciidoctor.org/) が出力するHTMLファイルの体裁をCSSを使って仕様書らしく整える方法について紹介します。



## おおまかな手順

おおまかな手順を下記に示します。

1. Asciidoctorのインストール
2. コーディングの準備
3. コーディング
4. HTMLファイルの出力
5. 動作確認



## Asciidoctorのインストール

Asciidoctorをインストールする手順については [AsciidoctorのInstallationセクション](https://asciidoctor.org/#installation) で詳しく説明されています。macOSの場合は下記のコマンドでインストールが可能です。

```shell
brew install asciidoctor
```



## コーディングの準備

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir asciidoctor-requirement
cd asciidoctor-requirement
touch requirement.adoc
touch style.css
```



## コーディング

### requirement.adoc

エディタでrequirement.adocを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/698121f677adda1266d3cd8217bc23e8?file=requirement.adoc.txt)

### style.css

続いてエディタでstyle.cssを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/698121f677adda1266d3cd8217bc23e8?file=style.css)



## HTMLファイルの出力

下記のコマンドを実行してHTMLファイルを出力します。

```shell
asciidoctor -a stylesheet=style.css -o dist/requirement.html requirement.adoc
```




## 動作確認

下記のコマンドを実行するなどして出力されたHTMLファイルを開きます。

```shell
open dist/requirement.html
```

ブラウザの印刷機能を利用すればPDF出力も可能です。[Firefox](https://www.mozilla.org/firefox/) はフッターにページ番号とページ総数を表示する機能を備えているのでおすすめです。

![1ページ目です、技術ブログ配信システム要件定義書の表紙です。](/images/articles/asciidoctor-requirement/check-01.png)

![2ページ目です、1.この文書の目的と構成のページです。](/images/articles/asciidoctor-requirement/check-02.png)

![3ページ目です、2.ページ構成のページです。](/images/articles/asciidoctor-requirement/check-03.png)

![4ページ目です、3.レイアウトのページです。](/images/articles/asciidoctor-requirement/check-04.png)

![5ページ目です、4.記事一覧のページです。](/images/articles/asciidoctor-requirement/check-05.png)

![6ページ目です、5.記事詳細のページです。](/images/articles/asciidoctor-requirement/check-06.png)

![7ページ目です、6.改訂記録のページです。](/images/articles/asciidoctor-requirement/check-07.png)



## おわりに

Asciidoctorでは図表に番号やキャプションを入れられるなど細かい部分で「あ、嬉しい！」と感じることが多いです。シンプルなMarkdownも素晴らしいですが文書作成には多機能なAsciidoctorの方が適しているように感じます。

別の記事で [Puppeteerでスクリーンショットを撮影する方法](https://zenn.dev/tatsuyasusukida/articles/puppeteer-screenshot) を紹介しており、要件定義書に画面例を掲載したい時などにスクリーンショット撮影を自動化するのに役立ちます。興味がありましたら併せてご覧いただければ幸いです。

この記事がWordで仕様書を書くことにもう疲れた方のお役に立てれば幸いです。最後まで読んでいただきありがとうございました！
