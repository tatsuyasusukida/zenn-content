---
title: "Gitで複数のコミットを後から1つにまとめる方法"
emoji: "😸"
type: "tech"
topics: ["git"]
published: true
---



# この記事について

この議事では `git rebase -i` コマンドを使って複数のコミットを後から1つにまとめる方法を紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. 複数のコミットを作成する（準備）
2. 複数のコミットを1つにまとめる



# 複数のコミットを作成する

下記のコマンドを実行して複数のコミットを作成して準備します。

```shell
mkdir git-rebase-interactive
cd git-rebase-interactive
git init
touch one
git add .
git commit -m "one"
touch two
git add .
git commit -m "two"
touch three
git add .
git commit -m "three"
git log --oneline
```

実行結果を下記に示します。

```
$ mkdir git-rebase-interactive
$ cd git-rebase-interactive
$ git init
Initialized empty Git repository in /Users/susukida/workspace/tech-blog/articles/.git/
$ touch one
$ git add .
$ git commit -m "one"
[main (root-commit) e4a7a52] one
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 one
$ touch two
$ git add .
$ git commit -m "two"
[main 342ac05] two
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 two
$ touch three
$ git add .
$ git commit -m "three"
[main de65506] three
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 three
$ git log --oneline
de65506 (HEAD -> main) three
342ac05 two
e4a7a52 one
```



# 複数のコミットを1つにまとめる

下記のコマンドを実行して複数のコミットを1つにまとめる作業を開始します。

```shell
git rebase -i --root
```

コマンドを実行するとvimなどのコマンドラインエディタが起動して下記のように表示されます。

```
pick e4a7a52 one
pick 342ac05 two
pick de65506 three

# Rebase de65506 onto fcfb889 (3 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
# .       create a merge commit using the original merge commit's
# .       message (or the oneline, if no original merge commit was
# .       specified); use -c <commit> to reword the commit message
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

下記のように2行目と3行目の `pick` を `squash` または `s` に変更してから保存して終了します。vimの場合は `ESC` `:` `w` `q` キーを押すと保存して終了することができます。

```
pick e4a7a52 one
squash 342ac05 two
squash de65506 three
```

コメントを編集するためのエディタが起動して下記のように表示されます。

```
# This is a combination of 3 commits.
# This is the 1st commit message:

one

# This is the commit message #2:

two

# This is the commit message #3:

three

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Mon Apr 25 19:23:59 2022 +0900
#
# interactive rebase in progress; onto fcfb889
# Last commands done (3 commands done):
#    squash 342ac05 two
#    squash de65506 three
# No commands remaining.
# You are currently rebasing branch 'main' on 'fcfb889'.
#
#
# Initial commit
#
# Changes to be committed:
#       new file:   one
#       new file:   three
#       new file:   two
#
```

そのままでもOKですが下記のように修正してから再び保存して終了します。

```
one two three
```

下記のように表示されたら成功です。

```
[detached HEAD 86e0875] one two three
 Date: Mon Apr 25 19:23:59 2022 +0900
 3 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 one
 create mode 100644 three
 create mode 100644 two
Successfully rebased and updated refs/heads/main.
```

下記のコマンドを実行して念のためログを確認します。

```shell
git log --oneline
```

下記のように表示されたら成功です。

```
86e0875 (HEAD -> main) one two three
```



# おわりに

コミットを後から1つにまとめることが気軽にできると、区切りの悪いタイミングでも躊躇なくコミットすることができます。この記事が皆さんのGitライフをますます快適にする一助になれば幸いです。最後まで読んでいただきありがとうございました！



# 関連記事

- [Gitで「これだけ知っていれば十分」というコマンド10選](https://zenn.dev/tatsuyasusukida/articles/git-enough-to-know)
- [GitHubのGitレポジトリにソースコードをプッシュする方法](https://zenn.dev/tatsuyasusukida/articles/github-git-push)
- [GistのGitレポジトリにソースコードをプッシュする方法](https://zenn.dev/tatsuyasusukida/articles/github-gist-push)
