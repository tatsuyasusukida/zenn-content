---
title: "GistのGitレポジトリにソースコードをプッシュする方法"
emoji: "🐱"
type: "tech"
topics: ["git", "github"]
published: true
---

# この記事について

この記事では [GitHub Gist](https://gist.github.com/) のGitレポジトリに既存のソースコードをプッシュする方法を紹介します。

Zennで技術ブログを書くためにGitHub Gistを使いはじめたのですが、GitHubのように空のレポジトリを作成してソースコードをプッシュする方法がわかリませんでした。試行錯誤の結果、同様のことを実現する方法を見つけたので同じことで困っている方の参考になれば幸いです。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. Gistの作成
3. Gitの設定
4. ソースコードのプッシュ



# コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir column-gist-push
cd column-gist-push
npm init -y
npm install --save dotenv
touch .gitignore
touch .env
touch main.js
```

エディタで.envを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/642aae4abfe889ee258c1489598f12ce?file=.env)

エディタで.gitignoreを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/642aae4abfe889ee258c1489598f12ce?file=.gitignore)

エディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/642aae4abfe889ee258c1489598f12ce?file=main.js)

コードを実行するには下記のコマンドを実行します。
（この記事ではソースコードをプッシュすることが目的なのでコードの実行は任意です）

```shell
node -r dotenv/config main.js
```



# Gistの作成

Gist（GitHub Gistのレポジトリ）を作成するにはWebブラウザで [GitHub Gist](https://gist.github.com/) のページへアクセスします。ログインしていない場合はページ右上に「Sign in」リンクが表示されるのでクリックしてログインします。

![GitHub Gistのトップページです。Gist descriptionやfilename including extensionの入力部に加え、Add fileボタンやCreate secret gistボタンを備えています。](/images/articles/gist-push/gist-01.png)

GitHubとは異なり、GitHub Gistでは空のレポジトリを作成することができない様子なのでとりあえず.gitignoreの内容をコピー＆ペーストしてGistを作成します。この際、Gist descriptionにコメント（例：Gist Push Example）を入力しておくと後から探すときに便利です。

![Gistを作成する直前の様子です。Gist descriptionに「Git Push Example」と入力し、filename including extensionsに「.gitignore」と入力し、ファイルの内容を入力するテキストエリアに「/node_modules/」と入力しています。](/images/articles/gist-push/gist-02.png)

ページ右下のCreate secret gistボタンの隣にある「▼」ボタンをクリックして表示されるドロップダウンメニューから「Create public gist」を選んでクリックするとCreate secret gistボタンがCreate public gistボタンに変化します。

![Create public gistを選んでいる様子です。ドロップダウンメニューにはCreate secret gistとCreate public gistの2つが含まれています。](/images/articles/gist-push/gist-03.png)

「Create public gist」ボタンをクリックするとGistが作成され、Gistのページが表示されます。

![Gistのページです。EditボタンやDeleteボタンを備えています。](/images/articles/gist-push/gist-04.png)

EditボタンやDeleteボタンの下あたりにある「Embed ▼」ボタンをクリックして表示されるドロップダウンメニューの中から「Clone via SSH」を選んでクリックします。

![Embedボタンをクリックしてドロップダウンメニューが表示されている様子です。ドロップダウンメニューにはClone via SSHが含まれています。](/images/articles/gist-push/gist-05.png)

Clone via SSH ▼ボタンの隣に「git@」から始まるGistへアクセスするための情報が表示されるので隣にあるコピーボタンを利用するなどしてコピーします。



# Gitの設定

ターミナルへ戻り、下記のコマンドを実行します。「git@gist.github.com:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.git」の部分は先ほどコピーしたものに置き換えます。

```shell
git init
git remote add gist git@gist.github.com:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.git
git fetch gist
git branch main gist/main
git log
```

`git log` の実行結果に「HEAD -> main」が含まれていることを確認します。

![ターミナルのスクリーンショットです。git log の出力にHEAD -> mainが含まれています。](/images/articles/gist-push/git-01.png)




# ソースコードのプッシュ

ターミナルで下記のコマンドを実行します。

```shell
git add .
git commit -m "Actual initial commit"
git push gist main
```

![ターミナルのスクリーンショットです。git push gist mainを実行するとmain -> mainなどプッシュされていることを伝えるメッセージが表示されています。](/images/articles/gist-push/push-01.png)



# おわりに

プログラミングに関する技術ブログを書く上で外部からソースコードを読み込む機能はほぼ必須と考えており、GitHub Gistの他にもCodePenやJSFiddleからソースコードを読み込む機能を備えているZennは素晴らしいサービスだと思います。

理想を言えばGitHubのGitレポジトリからソースコードを読み込めれば嬉しいのですが、Zennではできないみたいなので仕方ありません...

![ZennのMarkdown記法一覧のページです。「2022/04〜より、GitHub上のソースコードファイルを埋め込めるようになりました。」との記載があります。](/images/articles/gist-push/conclusion-01.png)

いや、できるのかよ普通に(笑)
やるじゃん [catnose](https://zenn.dev/catnose99) さん！行の指定までできるとか神！
そうなるとGistを使うことは当分無さそうなのでこの記事の存在意義が危ぶまれますが、この記事を書いたことでZennの新機能に気付けて良かったです。

最後まで読んでいただきありがとうございました！

