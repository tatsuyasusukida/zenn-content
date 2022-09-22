---
title: "Flutterで動画を収録する方法"
emoji: "📹"
type: "tech"
topics: ["flutter", "dart", "ios"]
published: true
---

## この記事について

この記事ではFlutterの[Camera](https://pub.dev/packages/camera)プラグインを使ってiOSアプリで動画を収録する方法について紹介します。内容としては下記2件の公式チュートリアルを合体させたものとなっています。

- [Take a picture using the camera](https://docs.flutter.dev/cookbook/plugins/picture-using-camera)
- [Play and pause a video](https://docs.flutter.dev/cookbook/plugins/play-video)

収録自体は成功したのですが、何度か収録を繰り返すと音声が入らなかったり最初の方に黒い画面が入ったりする現象が発生し、現状では原因も回避方法もわかっていません。このような未完成な状態で公開するのも恐縮ですが、再現手順の記録のために記事にまとめようと思います。

【追記｜2022年9月22日】
現象を回避する方法に関する記事を投稿しました。
[Flutterで動画収録時にiOS不具合を回避する方法](https://zenn.dev/tatsuyasusukida/articles/how-to-avoid-flutter-ios-video-recording-bug)



## コーディングの準備

VSCodeをお使いの場合はFlutter: New Projectコマンドを実行してプロジェクトを作成します。

プロジェクトが作成されたらターミナルで下記のコマンドを実行します。

```sh
flutter pub add camera path_provider path video_player
flutter pub get
touch lib/video_recorder_screen.dart
touch lib/video_player_screen.dart
```

ios/Runner.xcodeprojを開いて3カ所あるPRODUCT_BUNDLE_IDENTIFIERを修正します。

```
PRODUCT_BUNDLE_IDENTIFIER = com.loremipsum.VideoRecorder(日付8桁);
```

ios/Runner/Info.plistを開いて下記の内容を追加します。

```
<key>NSCameraUsageDescription</key>
<string>your usage description here</string>
<key>NSMicrophoneUsageDescription</key>
<string>your usage description here</string>
```



## コーディング

### main.dart

@[gist](https://gist.github.com/tatsuyasusukida/667a488796472c9e613b758254bfab50?file=main.dart)

### video_recorder_screen.dart

@[gist](https://gist.github.com/tatsuyasusukida/667a488796472c9e613b758254bfab50?file=video_recorder_screen.dart)

### video_player_screen.dart

@[gist](https://gist.github.com/tatsuyasusukida/667a488796472c9e613b758254bfab50?file=video_player_screen.dart)



## 動作確認

VSCodeのステータスバーのDevice SelectorエリアでiOS実機を選択します。

メニューからRun > Start Debuggingを選んでクリックしてデバッグを開始します。

iOS実機でアプリが起動してVideo recorder screenが表示されたら、フローティングアクションボタンを2回タップしてフロントカメラから動画を収録します。

動画の収録が完了してVideo player screenが表示されたら、フローティングアクションボタンをアップして収録した動画を再生します。



## 参考にしたWebページ

- [Tutorial: Video Recording and Replay with Flutter
](https://bettercoding.dev/flutter/tutorial-video-recording-and-replay/)
- [when using camera plugin to record video in ios, the first frame may be black](https://github.com/flutter/flutter/issues/57831)



## おわりに

CameraControllerを使い捨て（一度動画を収録したら廃棄する）にすることで現象を回避できるかも知れませんが現時点では未検証です。まだFlutterを使いはじめたばかりなので何とも言えませんが、カメラなどを使おうとしてハマってしまうと問題解決が難しい感じがしたので開発前の検証が重要だと感じました。Flutter自体は素晴らしいと思うのでSwiftUIやJetpack Composeと並行して学習を続けたいと思います。



## 追記｜2022年9月22日

何度か収録を繰り返すと音声が入らなかったり最初の方に黒い画面が入ったりする現象を回避する方法に関する記事を投稿しました。

[Flutterで動画収録時にiOS不具合を回避する方法](https://zenn.dev/tatsuyasusukida/articles/how-to-avoid-flutter-ios-video-recording-bug)