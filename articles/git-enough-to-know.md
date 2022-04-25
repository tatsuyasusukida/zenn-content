---
title: "Gitで「これだけ知っていれば十分」というコマンド10選"
emoji: "😽"
type: "tech"
topics: ["git"]
published: true
---



# この記事について

この記事ではGitでソースコードなどのバージョン管理を行うにあたって「これだけ知っていれば十分」というコマンドを紹介します。



# コマンド一覧

紹介するコマンドの一覧を下記に示します。

1. レポジトリを初期化する / git init
2. コミットの準備をする / git add .
3. コミットする内容を確認する / git status
4. コミットする / git commit -m "comment"
5. コミットの履歴を確認する / git log --all --graph
6. リモートレポジトリを追加する / git remote add origin git@github.com:tatsuyasusukida/repository-name.git
7. リモートに変更内容をアップロードする / git push origin main
8. リモートレポジトリをクローンする / git clone git@github.com:tatsuyasusukida/repository-name.git
9. リモートから変更内容をダウンロードする / git fetch origin
10. リモートの変更内容を取り込む / git merge origin/main



# レポジトリを初期化する

コマンド例を下記に示します。

```shell
git init
```

現在のディレクトリにGitレポジトリの作業ツリーにするためのコマンドです。内部的には.gitという名前のディレクトリが作成され、データや設定などが格納されます。



# コミットの準備をする

コマンド例を下記に示します。

```shell
git add .
```

ファイルの変更や追加をコミット対象に設定するコマンドです。現在のディレクトリ以下のファイルだけがコミット対象になるので `git init` を実行したディレクトリで実行するのがおすすめです。



# コミットする内容を確認する

コマンド例を下記に示します。

```shell
git status
```

コミット前にコミットする内容を確認するコマンドです。コミットしたいファイルが含まれていなかったり、コミットしたくないファイルが含まれていたりすることがあるので、なるべくコミットする前に実行するのがおすすめです。



# コミットする

コマンド例を下記に示します。

```shell
git commit -m "comment"
```

`git add .` でコミット対象に設定したファイルを実際にコミットするコマンドです。-mオプションはコメントを指定するオプションです。-mオプションがないとコマンドラインのエディタが起動し、不慣れな方は悲しい気持ちになるのでなるべく指定するのがおすすめです。



# コミットの履歴を確認する

コマンド例を下記に示します。

```shell
git log --all --graph
```

コミットの日時やコメントなどのログを確認することができます。確認を終了するにはターミナルを閉じるのではなく `q` キーを押します。



# リモートレポジトリを追加する

コマンド例を下記に示します。

```shell
git remote add origin git@github.com:tatsuyasusukida/repository-name.git
```

[GitHub](https://github.com/) や [GitHub Gist](https://gist.github.com/) などのリモートレポジトリを追加するコマンドです。リモートレポジトリを追加することで、変更内容のfetch（ダウンロード）やpush（アップロード）ができるようになります。リモートレポジトリのアドレス（originの後のgit@からはじまる）部分を間違えてしまった場合はaddの部分をset-urlに変えてコマンドを実行します。



# リモートに変更内容をアップロードする

コマンド例を下記に示します。

```shell
git push origin main
```

`git remote add origin` コマンドを実行して設定したリモートレポジトリに変更内容をpush（アップロード）します。ここまでに紹介した7つのコマンドを知っていれば1人で1台のPCを使って作業する場合は十分です。




# リモートレポジトリをクローンする

コマンド例を下記に示します。

```shell
git clone git@github.com:tatsuyasusukida/repository-name.git
```

repository-nameというディレクトリを作成したレポジトリとして初期化した後、リモートレポジトリから変更内容をダウンロードして作業ツリーとして展開します。複数と共同して作業する時や1人で複数のPCを使って作業する時などに活躍します。



# リモートから変更内容をダウンロードする

コマンド例を下記に示します。

```shell
git fetch origin
```

リモートレポジトリから変更内容をダウンロードします。ダウンロードするだけで作業ツリーには反映されないので安心して実行できます。



# リモートの変更内容を取り込む

コマンド例を下記に示します。

```shell
git merge origin/main
```

`git fetch origin` コマンドを実行してダウンロードしたリモートレポジトリの変更内容を作業ツリーにmerge（取り込み）して反映します。mergeが一発で成功することを心よりお祈り申し上げます。



# おわりに

この記事ではカバーできませんでしたが、この他にも知っていた方が良いと考える点について下記に示します。

- `git branch` でブランチを作成する方法
- `git checkout` でブランチを切り替える方法
- .gitignoreファイルを使ってコミット対象から除外するファイルを指定する方法
- GitHubやGitHub Gistでレポジトリを作成する方法

不足している点などありましたらお気軽にコメントをいただければ幸いです。
最後まで読んでいただきありがとうございました！



# 関連記事

- [GitHubのGitレポジトリにソースコードをプッシュする方法](https://zenn.dev/tatsuyasusukida/articles/github-git-push)
- [GistのGitレポジトリにソースコードをプッシュする方法](https://zenn.dev/tatsuyasusukida/articles/github-gist-push)



# 追記

変更点を表示する方法について言及していないと気づいたので追記します。`git add .` コマンドを実行する前の場合は下記のコマンドを実行します。

```shell
git diff
```

`git add .` コマンドを実行した後の場合は下記のコマンドを実行します。

```shell
git diff --cached
```
