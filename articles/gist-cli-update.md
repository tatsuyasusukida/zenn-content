---
title: "ターミナルからGistを更新する方法"
emoji: "🐈"
type: "tech"
topics: ["git", "github", "gist"]
published: true
---

## この記事について

この記事では[gist](https://github.com/defunkt/gist)コマンドを使ってターミナルからGistを更新する方法について紹介します。



## gistコマンドのインストール

macOSでgistコマンドをインストールするには下記のコマンドを実行します。

```shell
brew install gist
```



## gistコマンドの認可

下記のコマンドを実行して認可の手続きを開始します。

```shell
gist --login
```

実行結果の例を下記に示します。

```
questing login parameters...
Please sign in at https://github.com/login/device
  and enter code: XXXX-XXXX
```

https://github.com/login/device にアクセスして8桁のコードを入力してからConfirmボタンをクリックします。

Authorize The gist gemのページが表示されたらAuthorize Conradlrwinボタンをクリックします。

ターミナルに下記のメッセージが出力されることを確認します。

```
Success! https://github.com/settings/connections/applications/4f7ec0d4eab38e74384e
```

### 参考画像

![](/images/articles/gist-cli-update/img-authorize-01.png)

![](/images/articles/gist-cli-update/img-authorize-02.png)

![](/images/articles/gist-cli-update/img-authorize-03.png)



## Gistの更新

Gistを更新するには下記のコマンドを実行します。

```shell
gist -u $GIST_ID file1 file2 file3
```

下記のように.envにGIST_IDを書いておくと便利です。

```:.env
GIST_ID="00000000000000000000000000000000"
```

コマンドを実行するときに.envを読み込むには下記のように書きます。

```shell
source .env && gist -u $GIST_ID file1 file2 file2
```



## おまけ：Gistの作成

Gistを作成するには下記のコマンドを実行します。

```shell
gist -od "ここにGistの説明が入ります" file1 file2 file3
```

なお、GitHub公式のコマンドラインツールである[gh](https://cli.github.com/)を使う場合は下記のコマンドを実行します。

```shell
gh gist -wpd "ここにGistの説明が入ります"file1 file2 file3
```



## おわりに

ghコマンドでも`gh gist edit $GIST_ID`と実行することで対話的に編集することが可能ですが、更新したい場合はgistコマンドが便利です。また、ソースコードはGitHubで管理したいけど共有はGistでしたいなど、GitHubとGistを併用したい時もgistコマンドはおすすめです。
