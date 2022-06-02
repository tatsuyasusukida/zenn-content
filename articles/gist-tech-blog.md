---
title: "Gistを技術ブログのように使うコツ7選"
emoji: "🎥"
type: "tech"
topics: ["gist"]
published: false
---

## この記事について

この記事では [GitHub Gist](https://gist.github.com/) を技術ブログのように使うためのコツを7つ紹介します。

- README.mdを最初に表示する
- ソースコードにリンクする
- 画像を表示する
- 動画を表示する
- デモを表示する
- タイトルをつける
- Gistページにリンクする

関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5#file-index-html)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5)


## README.mdを最初に表示する

!README.mdのようにファイル名を感嘆符(!)から始めることでGistページの最初に表示することができます。また、!README-gist-tech-blog.mdのようにスラッグを含めることでGistで検索するときに少し便利になります。ターミナルで感嘆符から始まる名前を持つファイルを作成するコマンドを下記に示します。

```shell
touch \!README-gist-tech-blog.md
```



## ソースコードにリンクする

GistではMarkdownにソースコードを埋め込むことができない様子です。代案としては同じGistレポジトリにソースコードを含めた上で下記のようなリンクを設ける方法が考えられます。

```md
[クリックしてindex.htmlに移動](#file-index-html)
```

下記に実行結果を示します。

[クリックしてindex.htmlに移動](https://gist.github.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5#file-index-html)



## 画像を表示する

Gistではターミナルからgitコマンドを使うことで画像ファイルをレポジトリに含めることができます。コマンド例を下記に示します。

```
git clone git@gist.github.com:0356b3a2ba3ae9b4885a33efe52b50a5.git gist-tech-blog
cd gist-tech-blog
cp ~/img-01.png ./
git add img-01.png
git commit -m "Add img-01.png"
git push origin main
```

レポジトリに含めた画像ファイルをMarkdownから参照するには下記のように記述します。

```md
![ここに画像ファイルの代替テキストが入ります](https://gist.github.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5/raw/img-01.png)
```

下記に実行結果を示します。

![ここに画像ファイルの代替テキストが入ります](https://gist.github.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5/raw/img-01.png)

1つの画像ファイルのサイズ、または、複数の画像ファイルのサイズの合計が1MBを超えるとソースコードが表示されなくなる様子です。画像を掲載したい場合は自前のGoogle Cloud Storageなどを利用する方が良いように思います。



## 動画を表示する

YouTubeの場合は下記のように記述することで動画のサムネイルを表示し、リンクを設定することができます。

```md
[![動画のサムネイル：ここに動画のタイトルが入ります](https://img.youtube.com/vi/GPINZB8ENUQ/0.jpg)](https://www.youtube.com/watch?v=GPINZB8ENUQ)
```

実行結果を下記に示します。

[![動画のサムネイル：ここに動画のタイトルが入ります](https://img.youtube.com/vi/GPINZB8ENUQ/0.jpg)](https://www.youtube.com/watch?v=GPINZB8ENUQ)



## デモを表示する

HTML限定ですがURLのgithubをgithackに置き換えることでデモを表示することができます。
HTMLファイルからCSSやJavaScriptを読み込むことも可能です。

```
[デモ](https://gist.githack.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5/raw/index.html)
```

下記に実行結果を示します。

[デモ](https://gist.githack.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5/raw/index.html)



## タイトルをつける

これは好みですが下記のようにREADMEの冒頭にタイトルをつけておくと記事の内容を思い出すのに便利です。また、絵文字があると後から探しやすくなります。

```md
# 😀 Gistを技術ブログとして使うコツ7選

## この記事について
...
```

GistレポジトリのdescriptionはそのままGistページのHTMLのtitleやdescriptionになる様子です。Googleなどの検索結果でタイトルがしっかり表示されるようにするため、Gistレポジトリのdescriptionにも同じタイトルをつけることがおすすめです。下記にコマンド例を示します。

```
gh gist create -wpd "😀 Gistを技術ブログとして使うコツ7選" *
```


## Gistページにリンクする

これも好みですが下記のようにREADMEの冒頭にGistページへのリンクを設けるとエディタからGistページへ移動できるようになるので便利です。

```md
- [ソースコード](https://gist.github.com/tatsuyasusukida/0356b3a2ba3ae9b4885a33efe52b50a5#file-index-html)
```

Gistレポジトリを作成しないとGistページのURLが確定しないので最初は下記のようにURL部分を空欄にしておくことになります。

```md
- [ソースコード]()
```



## おわりに

Gistは技術ブログの記事を書くために十分な機能を備えていると考えており、特にソースコードと記事を同じレポジトリで扱うことができる点やpushするだけで公開できる点はとても気に入っています。しかしながら、下記の点を考えるとGistを技術ブログとして使うのは少し気が引けます。

- ソースコードの埋め込みができない点
- アクセス解析ができない点
- 一覧を表示できない点（Gistのユーザーページはあるけれど...）
- スラッグを自分で設定できない点

技術ブログの記事にソースコードを埋め込むためにGistレポジトリを使うのであれば、記事の本文をメモ代わりにGistレポジトリに含めた方がお得な感じがするので私はなるべくそうするようにしています（2重管理になるのでそれはそれで面倒なのですが...）。

また、技術ブログ向けの記事をストックしておく用途にもGistはとても便利です。これからもGistを愛用しようと思います。この記事をお読みの方の中でGitHubやGistのMarkdownなどに関する便利なテクニックをご存知の方がいましたらご指導のコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！
