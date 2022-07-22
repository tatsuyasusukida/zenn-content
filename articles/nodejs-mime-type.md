---
title: "MIMEタイプのnpmパッケージ選びでもう迷わない"
emoji: "🧘"
type: "tech"
topics: ["nodejs", "mime", "express"]
published: true
---

## この記事について

この記事ではNode.jsでファイル名や拡張子からMIMEタイプを取得する必要がある時に[mime](https://www.npmjs.com/package/mime)と[mime-types](https://www.npmjs.com/package/mime-types)のどちらを使えばよいかいつも迷うので選定基準などについて紹介します。



## 結論

- expressを使っている場合はexpress.static.mime.lookupメソッドまたはres.typeメソッドを使う。
- `*/vnd.*`や`*/x-*`が不要であればmime/liteを使う。
- それ以外の場合はmimeを使う。



## express.static.mimeについて

[express](https://www.npmjs.com/package/express)は[serve-static](https://www.npmjs.com/package/serve-static)を使い、serve-staticは[send](https://www.npmjs.com/package/send)を使い、sendは[mime](https://www.npmjs.com/package/mime)を使うという依存関係があります。sendが使っているmimeのバージョンは2022年7月22日時点で1.6.0と現行の3.0.0と比べて古いです。MIMEタイプを取得するのに3.0.0の`mime.getType`メソッドを使いますが、1.6.0では`mime.lookup`メソッドを使うなどの違いがあります。mimeのバージョンが古くても不都合がない場合はexpress.static.mime.lookupメソッドを使うことで追加のnpmパッケージをインストールしなくてもよくなります。

なお、MIMEタイプを取得してContent-Typeヘッダに設定する場合はexpress.tatic.mime.lookupメソッドの代わりに(res.type)[https://expressjs.com/ja/api.html#res.type]メソッドを使います。



## mimeとmime-typesの比較

[mime](https://www.npmjs.com/package/mime)と[mime-types](https://www.npmjs.com/package/mime-types)はいずれもファイル名とMIMEタイプを相互変換するnpmパッケージですが、下記のような違いがあります。

- メソッド名が異なる。例えばmime-typesの`mime.lookup('json')`に対し、mimeでは`mime.getType('txt')`と書く。
- mimeの方がサイズが小さい。mime-typesのminified+compressedサイズが18KBであるのに対し、mimeは8KB。

詳細についてはmimeのGitHubページの[Mime .vs. mime-types .vs. mime-db modules](https://github.com/broofa/mime#mime-vs-mime-types-vs-mime-db-modules)セクションで説明されています。



## mime/liteについて

[mime/lite](https://github.com/broofa/mime#lite-version)は[mime](https://github.com/broofa/mime)の軽量版です。`*/vnd.*`や`*/x-*`が無い代わりにminified+compressedサイズが2KBであり、フルバージョンの8KBより軽量です。



## おわりに

同じようなライブラリがあるとどちらを使えば良いか迷いますよね。この記事をお読みの方の中で似たような経験がある方はお気軽にコメントをいただければ幸いです。最後までお読みいただきありがとうございました！
