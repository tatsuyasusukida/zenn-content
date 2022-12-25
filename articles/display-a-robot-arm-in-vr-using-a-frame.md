---
title: "A-Frameを使ってロボットアームをVR空間に表示する方法"
emoji: "🦾"
type: "tech"
topics: ["aframe", "vr", "robot"]
published: true
---

## この記事について

この記事では VR コンテンツを制作するための Web フレームワークである [A-Frame](https://aframe.io/) を使ってロボットアームを表示する方法を紹介します。

![](/images/articles/display-a-robot-arm-in-vr-using-a-frame/about-01.gif)

デモは下記のページからご覧になれます。

https://gist.githack.com/tatsuyasusukida/9fc8a8a775f876589ba95542e86b2630/raw/demo.html



## コーディングの準備

ターミナルで下記のコマンドを実行してディレクトリやファイルを作成します。

```sh
mkdir vr-robot-arm
cd vr-robot-arm
touch index.html
```



## 3D モデルのダウンロード

VR 空間に表示するロボットアームの 3D モデルを用意します。3D モデルは何でも良いですが、今回は [Free3D](https://free3d.com/) から下記のロボットアームの 3D モデルをダウンロードして使わせてもらいます。

![](/images/articles/display-a-robot-arm-in-vr-using-a-frame/download-01.png)

https://free3d.com/3d-model/industrial-robot-arm-53669.html

ダウンロードした Zip ファイルの中には `Rmk3.c4d` と `Rmk3.obj` の2つのファイルが含まれていますが、必要になるのは後者の `Rmk3.obj` だけです。



## OBJ → glTF への変換

ダウンロードした 3D モデルのデータ形式は OBJ ですが A-Frame では [glTF](https://www.khronos.org/gltf/) を使うことが推奨されています。

> We recommend glTF for distributing assets in production over the web. 

OBj → glTF に変換するには [obj2gltf](https://www.npmjs.com/package/obj2gltf) が必要になるので、ターミナルで下記のコマンドを実行してインストールします。

```sh
npm install -g obj2gltf
```

続いて下記のコマンドを実行して OBj → glTF に変換します。

```sh
obj2gltf Rmk3.obj -o robot-arm.gltf
```



## コーディング

エディタで index.html を開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/9fc8a8a775f876589ba95542e86b2630?file=demo.html)

ポイントを下記に示します。

- `a-assets` と `a-asset-item` を使って 3D モデルを読み込みます。
- `a-gltf-model` を使って 3D モデルを表示します。この際、`src` で表示する 3D モデルを指定します。
- `position` や `scale` を使って位置やサイズを調整します。



## 動作確認

ファイルをブラウザで開くと CORS エラーが発生するのでターミナルで下記のコマンドを実行して HTTP サーバーを起動します。

```sh
python3 -m http.server
```

ブラウザで http://localhost:8000/ にアクセスしてロボットアームが表示されることを確認します。

![](/images/articles/display-a-robot-arm-in-vr-using-a-frame/check-01.gif)



## おわりに

A-Frame を初めて使いましたが驚くほど簡単にロボットアームを VR 空間に表示できて感銘を受けました。まだ [A-Frame 公式ドキュメント](https://aframe.io/docs/) をほとんど読んでいないので Introduction だけでも一読しようと思います。A-Frame は [three.js](https://threejs.org/) や [WebXR Device API](https://developer.mozilla.org/docs/Web/API/WebXR_Device_API) を内部で使っているようなので、余力があればこれらについても学びたいと思います。
