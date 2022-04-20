---
title: "GitHubのGitレポジトリにソースコードをプッシュする方法"
emoji: "😺"
type: "tech"
topics: ["git", "github"]
published: true
---

# この記事について

この記事では [GitHub](https://github.com/) に空のGitレポジトリを作成してからローカルのGitレポジトリをする方法を紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. ローカルのGitレポジトリの作成
2. GitHubのGitレポジトリの作成
3. Gitの設定
5. ソースコードのプッシュ



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-github-push
cd column-github-push
touch main.js
```

エディタでmain.jsを開いて下記の内容を入力します。

https://github.com/tatsuyasusukida/column-github-push/blob/main/main.js

コードを実行するには下記のコマンドを実行します。
（この記事ではソースコードをプッシュすることが目的なのでコードの実行は任意です）

```shell
node main.js
```



# ローカルのGitレポジトリの作成

ターミナルで下記のコマンドを実行してGitレポジトリを作成します。

```shell
git init
git add .
git status
git commit -m "Initial commit"
```

![上記のコマンドを実行した直後のターミナルのスクーリンショットです。Initialized empty Git repositoryなど、Gitレポジトリが作成されていることを示すメッセージなどが表示されています。](/images/articles/github-git-push/local-01.png)



# GitHubのGitレポジトリの作成

Webブラウザで [GitHub](https://github.com/) にアクセスします。ログインしていない場合はページ右上に「Sign in」リンクが表示されるのでクリックしてログインします。

![GitHubのログインしていない状態のトップページです。ページ右上にSign inリンクを備えています。](/images/articles/github-git-push/remote-01.png)

GitHubのトップページの右上にある「＋ ▼」ボタンをクリックすると表示されるドロップダウンメニューの中から「New repository」を選んでクリックします。

![GitHubのトップページで右上にある「＋ ▼」ボタンをクリックした直後の状態です。ドロップダウンメニューが表示され、その中にNew repositoryリンクが含まれています。](/images/articles/github-git-push/remote-02.png)

Create a new repositoryページが表示されたら下記2点を入力します。

- Repository Name: レポジトリの名前（例：column-github-push）
- Description: レポジトリに関する説明（例：GitHub Push Example）

![GitHubのCreate a new repositoryページです。Repository Nameの入力部やDescriptionの入力部を備えています。](/images/articles/github-git-push/remote-03.png)

ページの最後の方にある「Create repository」ボタンをクリックしてGitレポジトリを作成します。

![GitHubのCreate a new repositoryページの最後の方にCreate repositoryボタンがあります。](/images/articles/github-git-push/remote-04.png)

Gitレポジトリの作成が完了し、Gitレポジトリのページが表示されたらQuick setupセクションに含まれている「git@」からはじまるGitHubのGitレポジトリにアクセスするための文字列をコピーします。コピーするには隣に表示されているコピーボタンをクリックするのが便利です。

![Gitレポジトリのページです。Quick setupセクションなどが含まれています。](/images/articles/github-git-push/remote-05.png)



# Gitの設定

ターミナルへ戻り、下記のコマンドを実行します。「git@github.com:xxxxxxxxxxxxxxx/yyyyyyyyyyyyyyyyyy.git」の部分は先ほどコピーしたものに置き換えます。

```shell
git remote add origin git@github.com:xxxxxxxxxxxxxxx/yyyyyyyyyyyyyyyyyy.git
git remote -v
```

`git remote -v` の実行結果に下記2点が含まれていることを確認します。

- origin git@github.com:xxxxxxxxxxxxxxx/yyyyyyyyyyyyyyyyyy.git (fetch)
- origin git@github.com:xxxxxxxxxxxxxxx/yyyyyyyyyyyyyyyyyy.git (push)

![上記のコマンドを実行した直後のターミナルのスクリーンショットです。git remote -vの実行結果に origin git@github.com:xxxxxxxxxxxxxxx/yyyyyyyyyyyyyyyyyy.git (fetch) と origin git@github.com:xxxxxxxxxxxxxxx/yyyyyyyyyyyyyyyyyy.git (push) の2点が含まれています。](/images/articles/github-git-push/git-01.png)




# ソースコードのプッシュ

ターミナルで下記のコマンドを実行します。

```shell
git push origin main
```

![上記のコマンドを実行した直後のターミナルのスクリーンショットです。実行結果に new branch main -> main などのメッセージが含まれており、GitHubのGitレポジトリでmainブランチが作成されていることがわかります。](/images/articles/github-git-push/push-01.png)

Gitレポジトリのページをリロードするとソースコードがプッシュされた様子を確認することができます。

![ソースコードをプッシュした直後のGitHubのGitレポジトリのページです。main.jsがファイル一覧に表示されており、ソースコードがプッシュされたことがわかります。](/images/articles/github-git-push/push-02.png)



# おわりに

GitHubは [GitHub Gist](https://gist.github.com/) というGitレポジトリサービスも提供しており、こちらはちょっとしたソースコードやメモの共有に適しています。

GitHub Gistでは空のレポジトリの作成ができなかったり、ディレクトリをサポートしていなかったりと制約が多いですが、技術ブログなどを書く時に記事への埋め込み（Embed）がしやすいなどのメリットがあります。

私たちの大好きなZennではもちろんGistの埋め込みに対応していますし、[Hugo](https://gohugo.io/) などの静的サイトジェネレーターもGistの埋め込みに対応しています（詳しくは [HugoのShortcodeに関するページ](https://gohugo.io/content-management/shortcodes/#gist) をご覧ください）。

[GitHub GistのGitレポジトリをどうにかしてGitHubのGitレポジトリのように扱う方法](https://zenn.dev/tatsuyasusukida/articles/github-gist-push) について別の記事で紹介していますので興味がありましたらご覧いただければ幸いです。
最後まで読んでいただきありがとうございました！

