---
title: "AWS RoboMaker でロボットのシミュレーションを実行する方法"
emoji: "🤖"
type: "tech"
topics: ["aws", "robot"]
published: false
---

## この記事について

4年ぶりくらいに [AWS RoboMaker](https://aws.amazon.com/robomaker/) を触ってみたところ公式ドキュメントの [Running your first simulation](https://docs.aws.amazon.com/robomaker/latest/dg/gettingstarted-first-sim.html) の説明があまり丁寧ではないので Hello World のシミュレーションを実行するのに3時間もかかってしまいました。この記事では他の方が時間を無駄にしないように AWS RoboMaker の Hello World を動かす方法について紹介します。

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

ターミナルで下記のコマンドを実行して Hello World の Git レポジトリをクローンし、vcstool を使って robot_wsソースコードをインポートします。

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



## レポジトリの作成

ターミナルで下記のコマンドを実行して Amazon ECR（AWS のコンテナイメージ保管サービス）のレポジトリを作成します。

```sh
aws ecr create-repository --repository-name $robotapp
aws ecr create-repository --repository-name $simapp
```



## コンテナイメージのプッシュ

ターミナルで下記のコマンドを実行してコンテナイメージをレポジトリにプッシュします。

```sh
docker tag $robotapp $ecruri/$robotapp
docker tag $simapp $ecruri/$simapp
aws ecr get-login-password --region $region | docker login --username AWS --password-stdin $ecruri
docker push $ecruri/$robotapp
docker push $ecruri/$simapp
```

公式ドキュメントではタグ作成時に下記のように `:latest` を末尾につけていますが、お使いのシェルによっては `atest` に変換されて失敗する可能性があるので `:latest` を末尾につけないようにします。

```sh
docker tag $robotapp $ecruri/$robotapp:latest
docker tag $simapp $ecruri/$simapp:latest
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

シミュレーションの実行に必要となる IAM ロールを作成します。IAM ロールを作成するために下記の手順にしたがって適当なシミュレーションジョブを作成します。

1. AWS の Web コンソールから RoboMaker のページにアクセスし、シミュレーションジョブの一覧ページへ移動する
2. シミュレーションジョブの一覧の右上にある「シミュレーションジョブの作成」をクリックしてシミュレーションジョブの設定ページへ移動する。
3. シミュレーションジョブの詳細セクションで IAM ロールとして「新しいロールを作成する」を選択し、IAM ロール名として `MyServiceRoleForRoboMaker` など適当な名前を入力してからページ右下の「次へ」ボタンをクリックしてロボットアプリケーションを指定するページへ移動する。
4. ロボットアプリケーションを選択し、起動コマンドに `/bin/bash, -c, sleep, 1` などを適当に入力してからページ右下の「次へ」ボタンをクリックしてシミュレーションアプリケーションを指定するページへ移動する。
5. シミュレーションアプリケーションを選択し、起動コマンドに `/bin/bash, -c, sleep, 1` などを適当に入力してからページ右下の「次へ」ボタンをクリックして確認と作成ページへ移動する。
6. ページ右下の「次へ」ボタンをクリックしてシミュレーションジョブを作成を開始する。

上記の手順にしたがって作成したシミュレーションジョブはしばらくするとステータスが「失敗」になりますが、もし失敗にならなければ手動でキャンセルします。キャンセルを忘れた場合はシミュレーションが8時間実行されて高額な請求が発生する恐れがありますのでご注意ください。



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
