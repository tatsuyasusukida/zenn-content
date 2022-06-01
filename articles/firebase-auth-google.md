---
title: "Firebase AuthでGoogleアカウントを使ってログインする方法【動画版あり】"
emoji: "🐥"
type: "tech"
topics: ["firebase", "auth"]
published: true
---

## この記事について

この記事ではFirebase Authを使って下記3点を行う方法について紹介します。

- Googleアカウントでサインイン（ログイン）する
- サインアウト（ログアウト）する
- ユーザーIDを取得する

関連リソースを下記に示します。

- [ソースコード](https://gist.github.com/tatsuyasusukida/bec4108846bbe41961917e616efc0981#file-app-js)
- [English version / 英語版](https://gist.github.com/tatsuyasusukida/bec4108846bbe41961917e616efc0981)
- [動画版](https://www.youtube.com/watch?v=mQJYmOxyvrE)

https://www.youtube.com/watch?v=mQJYmOxyvrE



## おおまかな手順

おおまか手順を下記に示します。

1. Firebaseプロジェクトの作成
2. Firebaseアプリの登録
3. Authenticationの有効化
4. コーディングの準備
5. コーディング
6. 動作確認



## Firebaseプロジェクトの作成

[FirebaseのWebコンソール](https://console.firebase.google.com/) にアクセスして「プロジェクトを追加」をクリックすると「プロジェクトの作成」の画面が表示されるので下記2点を入力します。

- プロジェクト名 (例: firebase-auth-google-日付8桁)

「自身の取引、ビジネス、仕事、または職業のみを目的としてFirebase を利用することを正式に認めます。」にチェックを入れてから「続行」ボタンをクリックします。

「Googleアナリティクス」の画面が表示されたら「このプロジェクトでGoogleアナリティクスを有効にする」のチェックを外してから「プロジェクトを作成」ボタンをクリックします。

プロジェクトの作成が完了したら「続行」ボタンをクリックします。

作成したプロジェクトの概要ページが表示されたら成功です。

### 参考画像

![](/images/articles/firebase-auth-google/img-project-01.jpg)

![](/images/articles/firebase-auth-google/img-project-02.jpg)

![](/images/articles/firebase-auth-google/img-project-03.jpg)

![](/images/articles/firebase-auth-google/img-project-04.jpg)

![](/images/articles/firebase-auth-google/img-project-05.jpg)



## Firebaseアプリの登録

プロジェクトの概要ページに表示されるプロジェクト名 (例: firebase-auth-google-日付8桁) の下にあるiOSやAndroidの隣にある「Web」ボタンをクリックします。

「ウェブアプリへのFirebaseの追加」画面が表示されたらアプリのニックネーム (例: firebase-auth-google-日付8桁-app) を入力してから「アプリ」を登録ボタンをクリックします。この際「このアプリのFirebase Hostingも設定します」のチェックを入れません。

アプリの登録が完了するとfirebaseConfigを含むサンプルコードが表示されるので後の手順のためにfirebaseConfigの内容を控えます。内容を控えるにはコピーボタンを利用するのが便利です。

firebaseConfigの内容を控え終わったら画面左上の「×」ボタンをクリックして「ウェブアプリへのFirebaseの追加」画面を閉じます。

### 参考画像

![](/images/articles/firebase-auth-google/img-app-01.jpg)

![](/images/articles/firebase-auth-google/img-app-02.jpg)

![](/images/articles/firebase-auth-google/img-app-03.jpg)

![](/images/articles/firebase-auth-google/img-app-04.jpg)

![](/images/articles/firebase-auth-google/img-app-05.jpg)



## Authenticationの有効化

プロジェクトの概要ページ左側のメニューに含まれる「Authentication」をクリックしてAuthenticationページに移動します。

Authenticationページに含まれる「始める」ボタンをクリックしてAuthenticationを有効化します。

Authenticationの有効化が完了したらのSign-in methodタブのログインプロバイダセクションに含まれる「Google」ボタンをクリックします。

設定画面が表示されたら「有効にする」トグルボタンをクリックして有効にした後、「プロジェクトのサポートメール」を選択してから「保存」ボタンをクリックします。

### 参考画像

![](/images/articles/firebase-auth-google/img-auth-01.jpg)

![](/images/articles/firebase-auth-google/img-auth-02.jpg)

![](/images/articles/firebase-auth-google/img-auth-03.jpg)

![](/images/articles/firebase-auth-google/img-auth-04.jpg)

![](/images/articles/firebase-auth-google/img-auth-05.jpg)

![](/images/articles/firebase-auth-google/img-auth-06.jpg)



## コーディングの準備

ターミナルで下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir firebase-auth-google
cd firebase-auth-google
npm init -y
npm install --save firebase
npm install --save-dev webpack webpack-cli webpack-dev-server
touch app.js firebase-config.json index.html webpack.config.js
```



## コーディング

### webpack.config.js

エディタでwebpack.config.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/bec4108846bbe41961917e616efc0981?file=webpack.config.js)

### index.html

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/bec4108846bbe41961917e616efc0981?file=index.html)

### firebase-config.json

エディタでfirebase-config.jsonを開いて先の手順で控えた内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/bec4108846bbe41961917e616efc0981?file=firebase-config.example.json)

### app.js

エディタでapp.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/bec4108846bbe41961917e616efc0981?file=app.js)

ポイントを下記に示します。

1. fetch関数などを使ってfirebase-configを読み込みます。
2. initializeApp関数を呼び出してアプリケーションを初期化します。
3. getAuth関数を呼び出してAuthenticationを初期化します。
4. onAuthStateChanged関数を使って認証の状態が変化した時に呼び出されるイベントハンドラを登録します。この例ではログインしているかどうかによってWebページに含まれるセクションの表示／非表示を切り替えているのに加え、user.uidにアクセスしてユーザーIDを取得しています。
5. ログインボタン（buttonSignin）をクリックした時にsignInWithRedirect関数を呼び出してGoogleのログインページへ移動します。
6. ログアウトボタン（buttonSignout）をクリックした時にsignOut関数を呼び出してログアウトします。



## 動作確認

ターミナルで下記のコマンドを実行します。

```shell
webpack serve
```

ブラウザで http://localhost:8080/ にアクセスしてログインセクションだけが表示されることを確認します。

「ログイン」ボタンをクリックするとGoogleのログインページに移動することを確認します。

ログインが完了するとアプリのページに戻り、ユーザーIDとログアウトボタンが表示されることを確認します。

「ログアウト」ボタンをクリックするとユーザー情報とログアウトのセクションが非表示なり、ログインセクションが表示されることを確認します。



## おわりに

Googleのログインページからアプリのページへ戻る時にクエリパラメータを使ってアクセストークンが渡されているのかなと思っていましたが、どうやらそうでもない様子でどういう仕組みになっているんだろうと興味津々です。公式ドキュメントに書いているのかしら。詳しい方がいらっしゃいましたらご指導のコメントをいただければ幸いです、その他のご意見ご感想のコメントも大歓迎です。最後までお読みいただきありがとうございました！



## 関連記事

- [Firebase Authを使ってメールアドレスとパスワードでログインする方法](https://zenn.dev/tatsuyasusukida/articles/firebase-auth-email)
