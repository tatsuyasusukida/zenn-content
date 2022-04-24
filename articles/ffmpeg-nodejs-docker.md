---
title: "DockerでFFmpegコマンドを利用できるNode.jsのコンテナイメージを作成する方法"
emoji: "📽"
type: "tech"
topics: ["ffmpeg", "nodejs", "docker"]
published: true
---



# この記事について

この記事ではFFmpegコマンドを利用できるNode.jsのコンテナイメージをDockerfileを使って作成する方法について紹介します。



# おおまかな手順

おおまかな手順を下記に示します。

1. コーディング
2. コンテナイメージの作成
3. 動作確認



# コーディング

下記のコマンドを実行してコーディングの準備をします。

```shell
mkdir ffmpeg-nodejs-docker
cd ffmpeg-nodejs-docker
touch Dockerfile main.js
```

エディタでDockerfileを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/30ccd85375fc7b2adcb3c97e7858cadf?file=Dockerfile)

ポイントを下記に示します。

1. `ENV LD_LIBRARY_PATH=/usr/local/lib` についてはコメントアウトしていますが環境によっては必要な場合もあるので動かない場合はお試しください。

続いてエディタでmain.jsを開いて下記の内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/30ccd85375fc7b2adcb3c97e7858cadf?file=main.js)

ポイントを下記に示します。

1. FFmpegの `-version` コマンドをを子プロセスとして起動しています。
2. FFmpegの標準出力と標準エラー出力が表示されるように設定しています。
3. FFmpegが終了した時に `resolve` を呼び出してPromiseに終了コードを送信してします。
4. FFmpegの実行中にエラーが発生した時に `reject` を呼び出してPromiseのエラーを通知しています。
5. FFmpegの終了コードを表示しています。



# コンテナイメージの作成

下記のコマンドを実行してコンテナイメージを作成します。

```shell
docker image build -t ffmpeg-nodejs-docker .
```

コマンドの実行結果を下記に示します。

```
docker image build -t ffmpeg-nodejs-docker .
[+] Building 0.1s (11/11) FINISHED                                              
 => [internal] load build definition from Dockerfile                       0.0s
 => => transferring dockerfile: 233B                                       0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for docker.io/library/node:lts-alpine         0.0s
 => [internal] load metadata for docker.io/jrottenberg/ffmpeg:4.1-alpine   0.0s
 => [ffmpeg 1/1] FROM docker.io/jrottenberg/ffmpeg:4.1-alpine              0.0s
 => [stage-1 1/4] FROM docker.io/library/node:lts-alpine                   0.0s
 => [internal] load build context                                          0.0s
 => => transferring context: 29B                                           0.0s
 => CACHED [stage-1 2/4] COPY --from=ffmpeg / /                            0.0s
 => CACHED [stage-1 3/4] WORKDIR /usr/src/app                              0.0s
 => CACHED [stage-1 4/4] COPY main.js ./                                   0.0s
 => exporting to image                                                     0.0s
 => => exporting layers                                                    0.0s
 => => writing image sha256:e76b59c0304c6f282d692d0182e23418395afbb1cc3e7  0.0s
 => => naming to docker.io/library/ffmpeg-nodejs-docker                    0.0s
```



# 動作確認

下記のコマンドを実行してFFmpegのバージョンが表示されることを確認します。

```shell
docker container run --rm ffmpeg-nodejs-docker
```

コマンドの実行結果を下記に示します。

```
ffmpeg version 4.1.8 Copyright (c) 2000-2021 the FFmpeg developers
built with gcc 10.2.1 (Alpine 10.2.1_pre1) 20201203
configuration: --disable-debug --disable-doc --disable-ffplay --enable-avresample --enable-fontconfig --enable-gpl --enable-libaom --enable-libass --enable-libbluray --enable-libfdk_aac --enable-libfreetype --enable-libkvazaar --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopenjpeg --enable-libopus --enable-libsrt --enable-libtheora --enable-libvidstab --enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxcb --enable-libxvid --enable-libzmq --enable-nonfree --enable-openssl --enable-postproc --enable-shared --enable-small --enable-version3 --extra-cflags=-I/opt/ffmpeg/include --extra-ldflags=-L/opt/ffmpeg/lib --extra-libs=-ldl --extra-libs=-lpthread --prefix=/opt/ffmpeg
libavutil      56. 22.100 / 56. 22.100
libavcodec     58. 35.100 / 58. 35.100
libavformat    58. 20.100 / 58. 20.100
libavdevice    58.  5.100 / 58.  5.100
libavfilter     7. 40.101 /  7. 40.101
libavresample   4.  0.  0 /  4.  0.  0
libswscale      5.  3.100 /  5.  3.100
libswresample   3.  3.100 /  3.  3.100
libpostproc    55.  3.100 / 55.  3.100
ffmpeg exit: code = 0
```



# おわりに

記事では `ffmpeg -version` コマンドしか実行していないので本当に動画のエンコードなどができるのかご不安な方は下記の手順をお試しください。

1. 動作確認用のサンプル動画を用意する。
2. コンテナ内でシェルを起動する。
3. コンテナ内で起動したシェルでFFmpegコマンドで動画をエンコードする。

動作確認用のサンプル動画についてはよろしければ下記をダウンロードしてお使いください。

https://gist.github.com/tatsuyasusukida/30ccd85375fc7b2adcb3c97e7858cadf/raw/82c56e16acce18d7e2f5e3bcc6a5c212093663d6/in.mp4

コンテナ内でシェルを起動するDockerコマンドは下記の通りです。

```shell
docker container run --rm -it -v `pwd`:/usr/src/app ffmpeg-nodejs-docker /bin/sh
```

ffmpeg

```shell
ffmpeg -i in.mp4 -max_muxing_queue_size 1024 out.mp4
```

実行結果は下記の通りです。

```
ffmpeg version 4.1.8 Copyright (c) 2000-2021 the FFmpeg developers
  built with gcc 10.2.1 (Alpine 10.2.1_pre1) 20201203
  configuration: --disable-debug --disable-doc --disable-ffplay --enable-avresample --enable-fontconfig --enable-gpl --enable-libaom --enable-libass --enable-libbluray --enable-libfdk_aac --enable-libfreetype --enable-libkvazaar --enable-libmp3lame --enable-libopencore-amrnb --enable-libopencore-amrwb --enable-libopenjpeg --enable-libopus --enable-libsrt --enable-libtheora --enable-libvidstab --enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxcb --enable-libxvid --enable-libzmq --enable-nonfree --enable-openssl --enable-postproc --enable-shared --enable-small --enable-version3 --extra-cflags=-I/opt/ffmpeg/include --extra-ldflags=-L/opt/ffmpeg/lib --extra-libs=-ldl --extra-libs=-lpthread --prefix=/opt/ffmpeg
  libavutil      56. 22.100 / 56. 22.100
  libavcodec     58. 35.100 / 58. 35.100
  libavformat    58. 20.100 / 58. 20.100
  libavdevice    58.  5.100 / 58.  5.100
  libavfilter     7. 40.101 /  7. 40.101
  libavresample   4.  0.  0 /  4.  0.  0
  libswscale      5.  3.100 /  5.  3.100
  libswresample   3.  3.100 /  3.  3.100
  libpostproc    55.  3.100 / 55.  3.100
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'in.mp4':
  Metadata:
    major_brand     : isom
    minor_version   : 512
    compatible_brands: isomiso2avc1mp41
    encoder         : Lavf58.76.100
  Duration: 00:00:08.00, start: 0.000000, bitrate: 145 kb/s
    Stream #0:0(und): Video: h264 (avc1 / 0x31637661), yuv420p, 1364x1014 [SAR 1:1 DAR 682:507], 9 kb/s, 0.50 fps, 0.50 tbr, 16384 tbn, 1 tbc (default)
    Metadata:
      handler_name    : VideoHandler
    Stream #0:1(und): Audio: aac (mp4a / 0x6134706D), 44100 Hz, stereo, fltp, 132 kb/s (default)
    Metadata:
      handler_name    : SoundHandler
Stream mapping:
  Stream #0:0 -> #0:0 (h264 (native) -> h264 (libx264))
  Stream #0:1 -> #0:1 (aac (native) -> aac (native))
Press [q] to stop, [?] for help
[libx264 @ 0x7f61c5448080] using SAR=1/1
[libx264 @ 0x7f61c5448080] using cpu capabilities: MMX2 SSE2Fast SSSE3 SSE4.2 AVX FMA3 AVX2 LZCNT BMI2
[libx264 @ 0x7f61c5448080] profile High, level 4.0
[libx264 @ 0x7f61c5448080] 264 - core 148 - H.264/MPEG-4 AVC codec - Copyleft 2003-2016 - http://www.videolan.org/x264.html - options: cabac=1 ref=3 deblock=1:0:0 analyse=0x3:0x113 me=hex subme=7 psy=1 psy_rd=1.00:0.00 mixed_ref=1 me_range=16 chroma_me=1 trellis=1 8x8dct=1 cqm=0 deadzone=21,11 fast_pskip=1 chroma_qp_offset=-2 threads=12 lookahead_threads=2 sliced_threads=0 nr=0 decimate=1 interlaced=0 bluray_compat=0 constrained_intra=0 bframes=3 b_pyramid=2 b_adapt=1 b_bias=0 direct=1 weightb=1 open_gop=0 weightp=2 keyint=250 keyint_min=1 scenecut=40 intra_refresh=0 rc_lookahead=40 rc=crf mbtree=1 crf=23.0 qcomp=0.60 qpmin=0 qpmax=69 qpstep=4 ip_ratio=1.40 aq=1:1.00
Output #0, mp4, to 'out.mp4':
  Metadata:
    major_brand     : isom
    minor_version   : 512
    compatible_brands: isomiso2avc1mp41
    encoder         : Lavf58.20.100
    Stream #0:0(und): Video: h264 (libx264) (avc1 / 0x31637661), yuv420p, 1364x1014 [SAR 1:1 DAR 682:507], q=-1--1, 0.50 fps, 16384 tbn, 0.50 tbc (default)
    Metadata:
      handler_name    : VideoHandler
      encoder         : Lavc58.35.100 libx264
    Side data:
      cpb: bitrate max/min/avg: 0/0/0 buffer size: 0 vbv_delay: -1
    Stream #0:1(und): Audio: aac (mp4a / 0x6134706D), 44100 Hz, stereo, fltp, 128 kb/s (default)
    Metadata:
      handler_name    : SoundHandler
      encoder         : Lavc58.35.100 aac
frame=    4 fps=0.0 q=-1.0 Lsize=     145kB time=00:00:08.01 bitrate= 148.2kbits/s speed=49.2x    
video:9kB audio:133kB subtitle:0kB other streams:0kB global headers:0kB muxing overhead: 1.962576%
[libx264 @ 0x7f61c5448080] frame I:1     Avg QP: 0.75  size:  4929
[libx264 @ 0x7f61c5448080] frame P:3     Avg QP:17.24  size:  1282
[libx264 @ 0x7f61c5448080] mb I  I16..4: 98.3%  0.0%  1.7%
[libx264 @ 0x7f61c5448080] mb P  I16..4:  0.0%  0.0%  0.4%  P16..4:  0.5%  0.0%  0.0%  0.0%  0.0%    skip:99.0%
[libx264 @ 0x7f61c5448080] 8x8 transform intra:0.0% inter:3.6%
[libx264 @ 0x7f61c5448080] coded y,uvDC,uvAC intra: 1.7% 0.6% 0.6% inter: 0.1% 0.0% 0.0%
[libx264 @ 0x7f61c5448080] i16 v,h,dc,p: 94%  4%  2%  0%
[libx264 @ 0x7f61c5448080] i8 v,h,dc,ddl,ddr,vr,hd,vl,hu: 25%  0% 75%  0%  0%  0%  0%  0%  0%
[libx264 @ 0x7f61c5448080] i4 v,h,dc,ddl,ddr,vr,hd,vl,hu: 40% 18% 24%  3%  3%  3%  4%  3%  3%
[libx264 @ 0x7f61c5448080] i8c dc,h,v,p: 97%  3%  0%  0%
[libx264 @ 0x7f61c5448080] Weighted P-Frames: Y:0.0% UV:0.0%
[libx264 @ 0x7f61c5448080] ref P L0: 96.3%  0.0%  2.5%  1.2%
[libx264 @ 0x7f61c5448080] kb/s:8.78
[aac @ 0x7f61c5445040] Qavg: 4120.144
```



## 関連記事

- [FFmpegで写真と音声から動画を作成する方法](https://zenn.dev/tatsuyasusukida/articles/ffmpeg-image-audio)
