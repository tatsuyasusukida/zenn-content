---
title: "AWS RoboMaker でロボットのシミュレーションを実行する方法"
emoji: "🤖"
type: "tech"
topics: ["aws", "robot"]
published: true
---

## この記事について

4年ぶりくらいに [AWS RoboMaker](https://aws.amazon.com/robomaker/) を触ってみたところ公式ドキュメントの [Running your first simulation](https://docs.aws.amazon.com/robomaker/latest/dg/gettingstarted-first-sim.html) の説明があまり丁寧ではないので Hello World のシミュレーションを実行するのに3時間もかかってしまいました。この記事では他の方が時間を無駄にしないように AWS RoboMaker の Hello World を動かす方法について紹介します。

![ロボットがY軸を中心にして回転している様子](/images/articles/aws-robomaker-tutorial/about-01.gif)

この記事は下記のスクラップを整理して作成しましたので説明が不足している点などがありましたらスクラップをご参照ください。

https://zenn.dev/tatsuyasusukida/scraps/b284d238faace6




## 前提条件

シミュレーションを実行するには下記3点のインストールが必要です。

- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
- [Docker](https://docs.docker.com/get-docker/)
- [vcstool](https://wiki.ros.org/vcstool)

vcstool をインストールするにはターミナルで下記のコマンドを実行します。

```sh
pip3 install vcstool
```



## ソースコードの取得

ターミナルで下記のコマンドを実行して Hello World の Git リポジトリをクローンし、vcstool を使って robot_wsソースコードをインポートします。

```sh
git clone https://github.com/aws-robotics/aws-robomaker-sample-application-helloworld.git helloworld
cd helloworld
vcs import robot_ws < robot_ws/.rosinstall
vcs import simulation_ws < simulation_ws/.rosinstall
```

vcstool コマンドの実行でパスが通っていなくてエラーになる場合はフルパスを使用します。下記は私の場合の例です。

```sh
/Library/Frameworks/Python.framework/Versions/3.7/bin/vcs import robot_ws < robot_ws/.rosinstall
/Library/Frameworks/Python.framework/Versions/3.7/bin/vcs import simulation_ws < simulation_ws/.rosinstall
```

コマンド実行後、`helloworld/robot_ws/src/deps` と `helloworld/simulation_ws/src/deps` が作成されたことを確認します。



## ロボットアプリのビルド

ターミナルで下記のコマンドを実行してロボットアプリをビルドします。

```sh
DOCKER_BUILDKIT=1 docker build . \
  --build-arg ROS_DISTRO=melodic \
  --build-arg LOCAL_WS_DIR=./robot_ws \
  --build-arg APP_NAME=helloworld-robot-app \
  -t robomaker-helloworld-robot-app
```

ビルド完了後、`docker images | grep robomaker-helloworld-robot-app` を実行してコンテナイメージの存在を確認します。

```
robomaker-helloworld-robot-app   latest   87b60a76fc2c   17 seconds ago   2.79GB
```

私の場合はビルドが完了するのに5分くらいの時間がかかりました。



## シミュレーションアプリのビルド

ターミナルで下記のコマンドを実行してシミュレーションアプリをビルドします。

```sh
DOCKER_BUILDKIT=1 docker build . \
  --build-arg GAZEBO_VERSION=gazebo-9 \
  --build-arg ROS_DISTRO=melodic \
  --build-arg LOCAL_WS_DIR=./simulation_ws \
  --build-arg APP_NAME=helloworld-sim-app \
  -t robomaker-helloworld-sim-app
```

ビルド完了後、`docker images | grep robomaker-helloworld-sim-app` を実行してコンテナイメージの存在を確認します。

```
robomaker-helloworld-sim-app   latest   3164220eb798   21 seconds ago   2.78GB
```

私の場合はビルドが完了するのに2.5分くらいの時間がかかりました。



## 環境変数の設定

ターミナルで下記のコマンドを実行して環境変数を設定します。

```sh
export robotapp=robomaker-helloworld-robot-app
export simapp=robomaker-helloworld-sim-app
export account=000000000000
export region=ap-northeast-1
export ecruri=$account.dkr.ecr.$region.amazonaws.com
```

000000000000の部分はご自身の AWS アカウント ID で置き換えます。



## リポジトリの作成

ターミナルで下記のコマンドを実行して Amazon ECR（AWS のコンテナイメージ保管サービス）のリポジトリを作成します。

```sh
aws ecr create-repository --repository-name $robotapp
aws ecr create-repository --repository-name $simapp
```



## プッシュの準備

ターミナルで下記のコマンドを実行してコンテナイメージをプッシュする準備をします。

```sh
docker tag $robotapp $ecruri/$robotapp
docker tag $simapp $ecruri/$simapp
```

公式ドキュメントではタグ作成時に下記のように `:latest` を末尾につけていますが、お使いのシェルによっては `atest` に変換されて失敗する恐れがあるので `:latest` を末尾につけないようにします。

```sh:失敗する恐れがあるので実行しない
docker tag $robotapp $ecruri/$robotapp:latest
docker tag $simapp $ecruri/$simapp:latest
```



## コンテナイメージのプッシュ

ターミナルで下記のコマンドを実行してコンテナイメージをリポジトリにプッシュします。

```sh
aws ecr get-login-password --region $region | docker login --username AWS --password-stdin $ecruri
docker push $ecruri/$robotapp
docker push $ecruri/$simapp
```

プッシュ完了後、ターミナルで下記のコマンドを実行して成功を確認します。

```sh
aws ecr list-images --repository-name $simapp
aws ecr list-images --repository-name $robotapp
```

実行結果を下記に示します。

```
$ aws ecr list-images --repository-name $simapp
{
    "imageIds": [
        {
            "imageDigest": "sha256:33ac1396bc04d7b0e1ea148a805088ea455ca7a471c5666ed09b9f3e334fdcc5",
            "imageTag": "latest"
        }
    ]
}

$ aws ecr list-images --repository-name $robotapp
{
    "imageIds": [
        {
            "imageDigest": "sha256:9bb17fbc7c4d92b2f59fe9e9870c9a5d0f2eee3f2df0c587b41daf49e447a4bf",
            "imageTag": "latest"
        }
    ]
}
```


## ロボットアプリケーションの登録

ターミナルで下記のコマンドを実行して AWS RoboMaker にロボットアプリケーションを登録します。

```sh
aws robomaker create-robot-application \
  --name my-robot-app \
  --robot-software-suite name=General \
  --environment uri=000000000000.dkr.ecr.ap-northeast-1.amazonaws.com/robomaker-helloworld-robot-app:latest
```

000000000000の部分はご自身の AWS アカウント ID で置き換えます。環境変数 `$simapp` を使いたいですが `:latest` が `atest` に変換される恐れがあるのでアドレスを直書きします。

登録が完了すると下記のような ARN が表示されるので後の手順のために控えます。

```
arn:aws:robomaker:ap-northeast-1:000000000000:robot-application/my-robot-app/1111111111111
```

ちなみにロボットアプリケーションの登録については公式ドキュメントの [Running your first simulation](https://docs.aws.amazon.com/robomaker/latest/dg/gettingstarted-first-sim.html) の手順リストから省かれています。



## シミュレーションアプリケーションの登録

ターミナルで下記のコマンドを実行して AWS RoboMaker にシミュレーションアプリケーションを登録します。

```sh
aws robomaker create-simulation-application \
  --name my-sim-app \
  --simulation-software-suite name=SimulationRuntime \
  --robot-software-suite name=General \
  --environment uri=000000000000.dkr.ecr.ap-northeast-1.amazonaws.com/robomaker-helloworld-sim-app:latest
```

繰り返しになりますが000000000000の部分はご自身の AWS アカウント ID で置き換えます。環境変数 `$simapp` を使いたいですが `:latest` が `atest` に変換される恐れがあるのでアドレスを直書きします。

登録が完了すると下記のような ARN が表示されるので後の手順のために控えます。

```
arn:aws:robomaker:ap-northeast-1:000000000000:simulation-application/my-sim-app/2222222222222
```

ちなみにシミュレーションアプリケーションの登録についても公式ドキュメントの [Running your first simulation](https://docs.aws.amazon.com/robomaker/latest/dg/gettingstarted-first-sim.html) の手順リストから省かれています。



## IAM ロールの作成

シミュレーションの実行に必要となる IAM ロールを作成します。IAM ロールを作成するためには下記の手順にしたがって適当なシミュレーションジョブを作成します。

1. AWS の Web コンソールから RoboMaker のページにアクセスし、シミュレーションジョブの一覧ページへ移動する。
2. シミュレーションジョブの一覧の右上にある「シミュレーションジョブの作成」をクリックしてシミュレーションジョブの設定ページへ移動する。
3. シミュレーションジョブの詳細セクションで IAM ロールとして「新しいロールを作成する」を選択し、IAM ロール名として `MyServiceRoleForRoboMaker` など適当な名前を入力してからページ右下の「次へ」ボタンをクリックしてロボットアプリケーションを指定するページへ移動する。
4. ロボットアプリケーションを選択し、起動コマンドに `/bin/bash, -c, sleep, 1` などを適当に入力してからページ右下の「次へ」ボタンをクリックしてシミュレーションアプリケーションを指定するページへ移動する。
5. シミュレーションアプリケーションを選択し、起動コマンドに `/bin/bash, -c, sleep, 1` などを適当に入力してからページ右下の「次へ」ボタンをクリックして確認と作成ページへ移動する。
6. ページ右下の「次へ」ボタンをクリックしてシミュレーションジョブを作成を開始する。

上記の手順にしたがって作成したシミュレーションジョブはしばらくするとステータスが「失敗」になりますが、もし失敗にならなければ手動でキャンセルします。キャンセルを忘れるとシミュレーションが8時間実行されて想定外のコストが発生する恐れがありますのでご注意ください。

### 参考画像

![](/images/articles/aws-robomaker-tutorial/role-01.png)

![](/images/articles/aws-robomaker-tutorial/role-02.png)

![](/images/articles/aws-robomaker-tutorial/role-03.png)

![](/images/articles/aws-robomaker-tutorial/role-04.png)

![](/images/articles/aws-robomaker-tutorial/role-05.png)

![](/images/articles/aws-robomaker-tutorial/role-06.png)



## シミュレーションの実行

`create_simulation_job.json` という名前でJSONファイルを作成して下記の内容を入力します。

```json:create_simulation_job.json
{
  "maxJobDurationInSeconds": 3600,
  "iamRole": "arn:aws:iam::000000000000:role/service-role/MyServiceRoleForRoboMaker",
  "robotApplications": [
      {
          "application": "arn:aws:robomaker:ap-northeast-1:000000000000:robot-application/my-robot-app/1111111111111",
          "applicationVersion": "$LATEST",
          "launchConfig": {
              "environmentVariables": {
                  "ROS_IP": "ROBOMAKER_ROBOT_APP_IP",
                  "ROS_MASTER_URI": "http://ROBOMAKER_ROBOT_APP_IP:11311",
                  "GAZEBO_MASTER_URI": "http://ROBOMAKER_SIM_APP_IP:11345"
              },
              "streamUI": false,
              "command": [
                  "/bin/bash", "-c", "roslaunch hello_world_robot rotate.launch"
              ]
          },
          "tools": [
              {
                  "streamUI": true,
                  "name": "robot-terminal",
                  "command": "/entrypoint.sh && xfce4-terminal",
                  "streamOutputToCloudWatch": true,
                  "exitBehavior": "RESTART"
              }
          ]
      }
  ],
  "simulationApplications": [
      {
          "application": "arn:aws:robomaker:ap-northeast-1:000000000000:simulation-application/my-sim-app/2222222222222",
          "launchConfig": {
              "environmentVariables": {
                "ROS_IP": "ROBOMAKER_SIM_APP_IP",
                "ROS_MASTER_URI": "http://ROBOMAKER_ROBOT_APP_IP:11311",
                "GAZEBO_MASTER_URI": "http://ROBOMAKER_SIM_APP_IP:11345",
                "TURTLEBOT3_MODEL":"waffle_pi"
              },
              "streamUI": true,
              "command": [
                  "/bin/bash", "-c", "roslaunch hello_world_simulation empty_world.launch --wait"
              ]
          },
          "tools": [
              {
                  "streamUI": true,
                  "name": "gzclient",
                  "command": "/entrypoint.sh && gzclient",
                  "streamOutputToCloudWatch": true,
                  "exitBehavior": "RESTART"
              }
          ]
      }
  ]
}
```

内容を入力したら下記の置き換えを行います。

- 000000000000 → ご自身の AWS アカウント ID
- 1111111111111 → 登録したロボットアプリケーションの ARN の末尾の数字列
- 2222222222222 → 登録したシミュレーションアプリケーションの ARN の末尾の数字列

ターミナルで下記のコマンドを実行してシミュレーションジョブを作成します。

```sh
aws robomaker create-simulation-job --cli-input-json file://create_simulation_job.json
```



## シミュレーション結果の表示

シミュレーション結果を表示するには下記の手順にしたがって gzclient に接続します。

1. AWS の Web コンソールから RoboMaker のページにアクセスし、シミュレーションジョブの一覧ページへ移動する。
2. シミュレーションジョブの一覧からステータスが実行中のシミュレーションジョブを探し、ID をクリックしてシミュレーションジョブの詳細ページへ移動する。
3. シミュレーションアプリケーションツールのセクションにある gzclient の「接続」ボタンをクリックして gzclient に接続する。

### 参考画像

![](/images/articles/aws-robomaker-tutorial/visualize-01.png)

![](/images/articles/aws-robomaker-tutorial/visualize-02.png)

![](/images/articles/aws-robomaker-tutorial/visualize-03.gif)



## シミュレーションジョブのキャンセル

シミュレーションジョブの詳細ページの右上にある「アクション」ボタンをクリックするとメニューが表示されるのでメニューの中からキャンセルを選んでクリックします。キャンセルを忘れるとシミュレーションが1時間実行されて想定外のコストが発生する恐れがありますのでご注意ください。

### 参考画像

![](/images/articles/aws-robomaker-tutorial/cancel-01.png)



## おわりに

プッシュの準備のセクションでも説明しましたが、公式ドキュメントではタグ作成のコマンドを下記のように記載しています。

```sh
docker tag $robotapp $ecruri/$robotapp:latest
docker tag $simapp $ecruri/$simapp:latest
```

私が使用している zsh では `$ecruri/$robotapp:latest` と `$ecruri/$simapp:latest` は下記のように展開されます。

```
000000000000.dkr.ecr.ap-northeast-1.amazonaws.com/robomaker-helloworld-robot-appatest
000000000000.dkr.ecr.ap-northeast-1.amazonaws.com/robomaker-helloworld-sim-appatest
```

上記のように `app:latest` となるべき部分が `appatest` になってしまっています。その結果、`docker push $ecruri/$robotapp` などを実行しても存在しないリポジトリにプッシュしようとしているので当然失敗します。しかも表示されるメッセージが下記のようにわかりにくいので原因を特定するのに苦労しました。

```
Using default tag: latest
The push refers to repository [000000000000.dkr.ecr.ap-northeast-1.amazonaws.com/robomaker-helloworld-robot-appatest]
c57a785c3150: Retrying in 1 second
2eaa8ffadd8a: Retrying in 1 second
a32a5f670a07: Retrying in 1 second
c17c7f190a63: Retrying in 1 second
153fc307cedb: Retrying in 1 second
807331bb8036: Waiting
26f69778b765: Waiting
5f70bf18a086: Waiting
d95c3384b170: Waiting
600b5d07f9c9: Waiting
760a8168331a: Waiting
aa598d36b395: Waiting
ed3220a3c03e: Waiting
7438ffc34197: Waiting
59549ba6b7f5: Waiting
06f8c1087691: Waiting
5a21724f143a: Waiting
ce86ca26af86: Waiting
9c97d367910b: Waiting
01ee26bfc3e7: Waiting
eee4459cdfd6: Waiting
45bbe3d22998: Waiting
EOF
```

"ecr push retrying" で検索すると下記の Stack Overflow の記事がヒットしてパーミッションの設定を変更すると解決するとの回答がありますが、真の原因はプッシュ先のリポジトリが存在しないことなのでいくら設定を変更しても解決しません。

https://stackoverflow.com/questions/70828205/pushing-an-image-to-ecr-getting-retrying-in-seconds

結局は自分の不注意のせいですが貴重な1〜2時間を空費してしまったことが悔やまれます。他の方が私と同じミスをしないことを祈ります。



## コメントをください

現在、テック系メタバースの構築に関わらせてもらっており、[A-Frame](https://aframe.io/) か [Unity](https://unity.com/) を使って下記のようなVRのWebコンテンツを制作したいと思っています。

- Webページに(a)VR空間の表示部、(b)コードの入力部、(c)実行ボタンの3つがある
- (a)VR空間の表示部にはロボットアームが表示される
- (b)コードの入力部にはロボットアームを制御するためのコードを入力する
- (c)実行ボタンを押すと入力されたコードに従ってロボットアームが動作する

制作したVRコンテンツは構築中のテック系メタバースの機能を手軽に体験してもらい、利用を促進するために活用したいと考えています。コンテンツの制作にあたって下記のような課題があります。

- ロボットアームのモデルをどうやって読み込むか？
- 入力したコードをVR空間にどうやって送るか？
- フロントエンド／バックエンドのどちらでシミュレーションを実行するか？
- 実行ボタンを押してから結果が表示されるまでの時間をどうやって短縮するか？
- 悪意のあるコードへの対策をどうするか？

フロントエンドで完結できれば理想的ですが、ロボットアームの制御には C や C++ などを利用すると思われるのでバックエンドも必要と予想しています。シミュレーションの完成度を高めようとするほどシミュレーション結果が表示されるまでの時間が長くなるので何かを妥協する必要がありますが、妥協しすぎるとバーチャルとリアルの乖離が大きくなるのでバランスが難しいです。

この記事をお読みの方の中でVRコンテンツ制作の経験や知見をお持ちの方がいましたらご指導をいただけますよう何卒よろしくお願い申し上げます。
