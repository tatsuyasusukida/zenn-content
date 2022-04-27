---
title: "SVGで原点を中心かつY軸を上向きにする方法"
emoji: "📈"
type: "tech"
topics: ["svg"]
published: true
---



# この記事について

この記事では [SVGのtransform属性](https://developer.mozilla.org/docs/Web/SVG/Attribute/transform) を使って原点を中心かつY軸を上向きにする方法を紹介します。ソースコードについては [この記事のGistページ](https://gist.github.com/tatsuyasusukida/1f5839de503ed3096b13cf7a36eda186) からダウンロード可能です。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir svg-origin-center
cd svg-origin-center
touch index.html
```

エディタでindex.htmlを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/1f5839de503ed3096b13cf7a36eda186?file=index.html)

ポイントを下記に示します。

1. g要素にtransform属性を設定して子要素すべてに座標変換を適用しています。translateは平行移動であり、原点を移動するために使っています。scaleは拡大縮小であり、Y軸の上向きにするために使っています。




# 動作確認

ブラウザでindex.htmlを開いて原点が中心かつY軸が上向きになっていることを確認します。

![ブラウザでindex.htmlを開いた様子です。Webページには原点が中心かつY軸が上向きのSVGが含まれており、枠線、Y軸線、X軸線に加え、おおむね原点を中心におおむね正三角形が描画されています。](/images/articles/svg-origin-center/check-01.png)



# おわりに

SVGはデフォルトでは原点が左上かつY軸が下向きですが、学校で習うグラフは原点が中心（少なくともY軸が上向き）であることが多いのでプログラミング中に間違えてしまうことが多くあります。原点の位置やY軸の方向を調整することで生産性が向上するようであれば、この記事で紹介した方法をご活用ください。この記事をお読みになった方の中でSVGの描画テクニックをご存知の方がいましたらお気軽にコメントをいただければ幸いです、励みになります。最後までお読みいただきありがとうございました！
