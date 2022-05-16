---
title: "Firebase Authでメールアドレスとパスワードを使ってログインする方法【動画版あり】"
emoji: "🔥"
type: "tech"
topics: ["firebase", "auth"]
published: true
---

## この記事について

この記事ではFirebase Authで下記4点を行う方法について紹介します。

- メールアドレスとパスワードでサインアップ（ユーザー登録）する
- メールアドレスとパスワードでサインイン（ログイン）する
- サインアウト（ログアウト）する
- ユーザーIDを取得する

関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/0de610fdd937aed64531cae3937f2c82#file-app-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/0de610fdd937aed64531cae3937f2c82)
- [動画版](https://www.youtube.com/watch?v=V6TxnE9jOLs)

https://www.youtube.com/watch?v=V6TxnE9jOLs



## おおまかな手順

おおまか手順を下記に示します。

1. Firebaseの設定
2. Firebaseアプリの登録
3. コーディング
4. 動作確認



## Firebaseプロジェクトの作成

[FirebaseのWebコンソール](https://console.firebase.google.com/) にアクセスして「プロジェクトを追加」をクリックすると「プロジェクトの作成」の画面が表示されるので下記2点を入力します。

- プロジェクト名 (例: firebase-auth-email-日付8桁)

「自身の取引、ビジネス、仕事、または職業のみを目的としてFirebase を利用することを正式に認めます。」にチェックを入れてから「続行」ボタンをクリックします。

「Googleアナリティクス」の画面が表示されたら「このプロジェクトでGoogleアナリティクスを有効にする」のチェックを外してから「プロジェクトを作成」ボタンをクリックします。

プロジェクトの作成が完了したら「続行」ボタンをクリックします。

作成したプロジェクトの概要ページが表示されたら成功です。



## Firebaseアプリの登録

プロジェクトの概要ページに表示されるプロジェクト名 (例: firebase-auth-email-日付8桁) の下にあるiOSやAndroidの隣にある「Web」ボタンをクリックします。

「ウェブアプリへのFirebaseの追加」画面が表示されたらアプリのニックネーム (例: firebase-auth-email-日付8桁-app) を入力してから「アプリ」を登録ボタンをクリックします。この際「このアプリのFirebase Hostingも設定します」のチェックを入れません。

アプリの登録が完了するとfirebaseConfigを含むサンプルコードが表示されるので後の手順のためにfirebaseConfigの内容を控えます。内容を控えるにはコピーボタンを利用するのが便利です。

firebaseConfigの内容を控え終わったら画面左上の「×」ボタンをクリックして「ウェブアプリへのFirebaseの追加」画面を閉じます。



## Authenticationの有効化

プロジェクトの概要ページ左側のメニューに含まれる「Authentication」をクリックしてAuthenticationページに移動します。

Authenticationページに含まれる始めるボタンをクリックしてAuthenticationを有効化します。

Authenticationの有効化が完了したらログインプロバイダセクションに含まれる「メール / パスワード」ボタンをクリックします。

設定画面が表示されたらメール / パスワードを「有効にする」トグルボタンをクリックして有効にしてから「保存」ボタンをクリックします。



## コーディング

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir firebase-auth-email
cd firebase-auth-email
npm init -y
npm install --save firebase
npm install --save-dev webpack webpack-cli webpack-dev-server
touch app.js firebase-config.json index.html webpack.config.js
```

## webpack.config.js

エディタでwebpack.config.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/0de610fdd937aed64531cae3937f2c82?file=webpack.config.js)

## index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/0de610fdd937aed64531cae3937f2c82?file=index.html)

## firebase-config.json

エディタでfirebase-config.jsonを開いて先の手順で控えた内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/0de610fdd937aed64531cae3937f2c82?file=firebase-config.example.json)

## app.js

エディタでapp.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/0de610fdd937aed64531cae3937f2c82?file=app.js)

ポイントを下記に示します。

1. fetch関数などを使ってfirebase-configを読み込みます。
2. initializeApp関数を呼び出してアプリケーションを初期化します。
3. getAuth関数を呼び出してAuthenticationを初期化します。
4. onAuthStateChanged関数を使って認証の状態が変化した時に呼び出されるイベントハンドラを登録します。この例ではログインしているかどうかによってWebページに含まれるセクションの表示／非表示を切り替えているのに加え、user.uidにアクセスしてユーザーIDを取得しています。
5. ログインボタン（buttonSignin）をクリックした時にsignInWithEmailAndPassword関数を呼び出してログインを試みます。
6. ユーザー登録ボタン（buttonSignup）をクリックした時にcreateUserWithEmailAndPassword関数を呼び出してユーザーの作成を試みます。
7. ログアウトボタン（buttonSignout）をクリックした時にsignOut関数を呼び出してログアウトします。



## 動作確認

ターミナルで下記のコマンドを実行します。

```shell
webpack serve
```

ブラウザで http://localhost:8080/ にアクセスしてログイン／ユーザー登録のセクションだけが表示されることを確認します。

メールアドレスとパスワードを入力してから「ユーザー登録」ボタンをクリックするとログイン／ユーザー登録のセクションが非表示になり、ユーザー情報とログアウトのセクションが表示されることを確認します。

「ログアウト」ボタンをクリックするとユーザー情報とログアウトのセクションが非表示なり、ログイン／ユーザー登録のセクションが表示されることを確認します。

「ログイン」ボタンをクリックすると同じuidが再度表示されることを確認します。



## おわりに

今更になってFirebaseを使い始めたのですが認証の部分はAuth0を使っているのでFirebase Authenticationを使うことはなかったのでこの記事を作成してみて勉強になりました。Firebase JavaScript SDK v9になってからAPIが大幅に変更になった様子で `import firebase from 'firebase/app'` ではなく、使いたい関数（例：initializeAppなど）だけを個別にインポートする必要があるのですね。それ自体はそれほど困らないのですがFirebaseの公式ドキュメントやGoogle検索でヒットする記事に古い情報と現在の情報が混在しているのは少し厄介ですね。いずれ時間が解決してくれることを祈ります。Firebaseに限らずGoogleは他社に比べてガンガンAPIを変えてくる傾向が強い気がします、そういうとこ好きだからGoogleを使っているのですが笑

Firebaseは日本でもアプリ開発者に人気があるようでたくさんの方がFirebaseに関する良い記事を日本語で書いてくださっているので色々と捗って助かります。私はどうにかカメラアプリを1つ作っただけでFirebaseについてはまだまだ初心者なので、先達の方々がまとめてくれた知見とチャーミングな言葉遣いのFirebase公式ドキュメント日本語版を頼りにこれからも精進したいと思います。Firebaseに関する良い書籍やWebサイトをご存知でしたらご指導のコメントをいただければ幸いです、その他のコメントも大歓迎です。最後までお読みいただきありがとうございました！



## 関連記事

- [Firebase AuthでGoogleアカウントを使ってログインする方法](https://gist.github.com/tatsuyasusukida/bec4108846bbe41961917e616efc0981)

