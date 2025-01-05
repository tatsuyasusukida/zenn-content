---
title: "自律開発 AI エージェント OpenHands を 5 ドルで試してみた"
emoji: "🖐️"
type: "tech"
topics: ["ai", "llm", "devin", "openhands"]
published: true
published_at: 2025-01-06 08:00
---

## この記事について

https://zenn.dev/ubie_dev/articles/devin-for-test

先日、[鹿野 壮](https://zenn.dev/tonkotsuboy_com)さんの記事を読んで、ソフトウェア開発エージェントの Devin に興味が湧きました。さっそく試してみたいと思いましたが、さすがに個人で 500 ドルを払うのは厳しかったので、同じような機能を持つと思われる OpenHands を試してみました。OpenHands を試すのにも 5 ドルは必要でしたが、100 分の 1 のコストでソフトウェア開発の未来を感じることができました。この記事では OpenHands を使って ToDo リストアプリを AI に開発してもらうまでの一連の流れを紹介します。

## Devin とは何か？

https://docs.devin.ai/

Devin は Cognition 社が提供するソフトウェア開発エージェントのサービスです。Devin はコーディングに加えてテストやリファクタリングなども自律的に行い、人間のプログラマーと同様に試行錯誤しながら問題解決ができます。公式ドキュメントによると次のようなことができるそうです。

- プルリクエストのレビュー
- 言語やバージョンの移行作業
- イシューへの対応
- Web アプリケーション開発
- 人間が開発に集中できるようにランチを注文する

## OpenHands とは何か？

https://github.com/All-Hands-AI/OpenHands

OpenHands は AI を活用したソフトウェア開発エージェントのプラットフォームです。Devin と同様、OpenHands も人間のプログラマーのように様々な作業を自律的に行うことができます。GitHub の README によると次のようなことができるそうです。

- コードの修正
- コマンドの実行
- Web の閲覧
- API の呼び出し
- StackOverflow からのコピペ

公式ドキュメントにある動画を見るとイメージしやすいと思います。

[![Hacker News のトップ記事を表示する bash スクリプトを OpenHands に作成してもらっている様子](/images/articles/try-openhands-for-5-dollars/openhands-01.png)
_https://docs.all-hands.dev/ より引用_](https://docs.all-hands.dev/img/teaser.mp4)

この動画では Hacker News のトップ記事を表示する bash スクリプトを OpenHands に作成してもらっています。

## Devin と OpenHands の違い

僕は次のように理解していますが、Devin を利用したことがなく OpenHands もまだ少ししか使っていないので、間違っていたらすいません。

|                          | Devin                                    | OpenHands                                                                                      |
| ------------------------ | ---------------------------------------- | ---------------------------------------------------------------------------------------------- |
| 提供形態                 | 商用サービス                             | オープンソースプロジェクト                                                                     |
| ホスティング             | ホスティング型のソリューション           | ユーザー自身がホスティングする必要あり（※）                                                    |
| コスト                   | 月額 500 ドルからの有料サービス          | ソフトウェア自体は無料だが、利用する LLM の API 料金（例: Claude 3.5 Sonnet、GPT-4）は別途必要 |
| ソースコードとライセンス | クローズドソース（ソースコードは非公開） | オープンソース（MIT ライセンスで公開）                                                         |

※ OpenHands も近いうちにホスティング型のサービスを提供されると思われます。

![AllHands の Join the Waitlist ページ](/images/articles/try-openhands-for-5-dollars/compare-01.png)
_AllHands の Join the Waitlist ページ_

## OpenHands を試す

前置きが長くなってしまいましたが、以下、OpenHands を使って ToDo アプリを作ってもらう流れについて説明します。

### 前提条件

- Docker バージョン 26.0.0 以上 or Docker Desktop バージョン 4.31.0 以上
- Linux または Mac OS（Windows ユーザーの方は WSL をお使いください）

### Anthropic API キーの取得

https://console.anthropic.com/ にアクセスしてサインインまたはサインアップします。

![Anthropic Console のサインインページ](/images/articles/try-openhands-for-5-dollars/api-01.png)
_Anthropic Console のサインインページ_

API Keys ページで Create Key ボタンを押して API キーを作成します。

![Anthropic Console の API Keys ページ](/images/articles/try-openhands-for-5-dollars/api-02.png)
_Anthropic Console の API Keys ページ_

API キーが作成されたら、クリップボードやエディタにコピーするなどして控えておきます。この API キーは OpenHands の起動後、AI プロバイダーの設定画面で利用します。

### Anthropic に 5 ドル課金

Billing ページで Complete setup ボタンを押します。

![Anthropic Console の Billing ページ](/images/articles/try-openhands-for-5-dollars/billing-01.png)
_Anthropic Console の Billing ページ_

フォームに必要事項を入力して、Continue ボタンを押します

![Anthropic Console の Billing 設定ページ](/images/articles/try-openhands-for-5-dollars/billing-02.png)
_Anthropic Console の Billing 設定ページ_

Initial credit purchase に $5 を入力してクレジットを購入します。

![Anthropic Console のクレジット購入ページ](/images/articles/try-openhands-for-5-dollars/billing-03.png)
_Anthropic Console のクレジット購入ページ_

### OpenHands の起動

https://docs.all-hands.dev/modules/usage/installation#start-the-app

ターミナルで下記のコマンドを実行し、OpenHands を起動します。

```sh:コマンド
export WORKSPACE_BASE=$HOME/OpenHands
docker pull docker.all-hands.dev/all-hands-ai/runtime:0.17-nikolaik
docker run -it --rm --pull=always \
    -e SANDBOX_RUNTIME_CONTAINER_IMAGE=docker.all-hands.dev/all-hands-ai/runtime:0.17-nikolaik \
    -e LOG_ALL_EVENTS=true \
    -e SANDBOX_USER_ID=$(id -u) \
    -e WORKSPACE_MOUNT_PATH=$WORKSPACE_BASE \
    -v $WORKSPACE_BASE:/opt/workspace_base \
    -v /var/run/docker.sock:/var/run/docker.sock \
    -v ~/.openhands-state:/.openhands-state \
    -p 3000:3000 \
    --add-host host.docker.internal:host-gateway \
    --name openhands-app \
    docker.all-hands.dev/all-hands-ai/openhands:0.17
```

下記のオプションを指定することで、ローカルのファイルシステムにマウントされるようになります。

- `-e SANDBOX_USER_ID=$(id -u)`
  - ホストとコンテナ間でユーザー ID を一致させ、権限の問題を回避します。
- `-e WORKSPACE_MOUNT_PATH=$WORKSPACE_BASE`
  - ホストの作業ディレクトリのパスをコンテナに環境変数として渡します。
- `-v $WORKSPACE_BASE:/opt/workspace_base`
  - ホストの作業ディレクトリをコンテナ内にマウントしてファイルを共有します。

https://docs.all-hands.dev/modules/usage/runtimes#connecting-to-your-filesystem

### API キーの設定

OpenHands のコンテナが起動したら http://localhost:3000/ にアクセスします。

![OpenHands の AI プロバイダー設定画面](/images/articles/try-openhands-for-5-dollars/configuration-01.png)
_OpenHands の AI プロバイダー設定画面_

API キーを入力してから Save ボタンを押します。

![OpenHands のトップページ](/images/articles/try-openhands-for-5-dollars/configuration-02.png)
_OpenHands のトップページ_

API キーの設定が完了するとプロンプトを入力できるようになります。

### ToDo アプリを作ってもらう

OpenHands のトップページで下記のプロンプトを入力して Enter キーを押します。あとは OpenHands が自動的にコードを生成してくれる様子を楽しみましょう。

```txt:プロンプト
Vite + React + TypeScript で基本的な ToDo リストアプリを作成してください。
フロントエンドだけで動作するようにして、全ての状態は localStorage に保存してください。
プロジェクトを作成するのに npx create vite を使わないでください。
コンポーネントを作成したらできるだけテストコードも作成してください。
テストケースの名前は日本語で書いてください。
テストフレームワークには Vitest を使ってください。
テストの実行コマンドには vitest run を使ってください。
サーバーはこちらで起動するのであなたが起動しなくて大丈夫です。
```

`npm run dev` や `vitest` のように終了しないコマンドを入力すると時間がかかる上に失敗することがあるので、プロンプトで言及しています。

### 試行錯誤の様子

長いのでアコーディオンで折りたたみましたが、興味がありましたらご覧ください。

![OpenHands が自律的に開発している様子](/images/articles/try-openhands-for-5-dollars/process-01.png)
_OpenHands が自律的に開発している様子_

:::details OpenHands の応答（全文）

> 承知しました。Vite + React + TypeScript で ToDo リストアプリを作成していきます。 まず、必要なファイル構造を作成し、パッケージをインストールしていきましょう。
> まずプロジェクトの基本構造を作成します：

```sh:コマンド
mkdir -p todo-app/src/components todo-app/src/hooks todo-app/src/__tests__
```

> package.json を作成します：

> TypeScript の設定ファイルを作成します：

> Vite の設定ファイルを作成します：

> テストのセットアップファイルを作成します：

```sh:コマンド
mkdir -p todo-app/src/test
```

> 必要なパッケージをインストールします：

```sh:コマンド
cd todo-app && npm install
```

> 次に、ToDo の型定義を作成します：

```txt:エラーメッセージ
Ran into [Errno 2] No such file or directory: '/workspace/todo-app/src/types/todo.ts' while trying to write to /workspace/todo-app/src/types/todo.ts
```

```sh:コマンド
mkdir -p todo-app/src/types
```

```txt:エラーメッセージ
Ran into [Errno 2] No such file or directory: '/workspace/todo-app/src/types/todo.ts' while trying to write to /workspace/todo-app/src/types/todo.ts
```

> 申し訳ありません。絶対パスで試してみましょう：

```sh:コマンド
pwd
```

```txt:エラーメッセージ
Ran into [Errno 2] No such file or directory: '/workspace/todo-app/src/types/todo.ts' while trying to write to /workspace/todo-app/src/types/todo.ts
```

> 申し訳ありません。一度ディレクトリに移動してから作成してみましょう：

```sh:コマンド
cd /workspace/todo-app && mkdir -p src/types && touch src/types/todo.ts
```

```txt:エラーメッセージ
Parameter `old_str` is required for command: str_replace.
```

```txt:エラーメッセージ
Parameter `old_str` is required for command: str_replace.
```

> 申し訳ありません。create コマンドを使用して再度試してみましょう：

```txt:エラーメッセージ
Invalid `path` parameter: /workspace/todo-app/src/types/todo.ts. File already exists at: /workspace/todo-app/src/types/todo.ts. Cannot overwrite files using command `create`.
```

> 申し訳ありません。一度ファイルを削除して作り直します：

```sh:コマンド
rm /workspace/todo-app/src/types/todo.ts && touch /workspace/todo-app/src/types/todo.ts
```

```txt:エラーメッセージ
Invalid `path` parameter: /workspace/todo-app/src/types/todo.ts. File already exists at: /workspace/todo-app/src/types/todo.ts. Cannot overwrite files using command `create`.
```

> 申し訳ありません。echo コマンドを使って直接書き込みましょう：

```sh:コマンド
echo 'export interface Todo {
  id: string;
  text: string;
  completed: boolean;
}' > /workspace/todo-app/src/types/todo.ts
```

> 次に、localStorage を扱うためのカスタムフックを作成します：

> ToDo コンポーネントを作成します：

> TodoList コンポーネントを作成します：

> メインの App コンポーネントを作成します：

> エントリーポイントを作成します：

> index.html を作成します：

> テストを作成します：

> テストを実行してみましょう：

```sh:コマンド
cd /workspace/todo-app && npm test
```

> これで、基本的な ToDo リストアプリケーションの実装が完了しました！主な機能は以下の通りです：
>
> - タスクの追加
> - タスクの削除
> - タスクの完了/未完了の切り替え
> - localStorage を使用したデータの永続化
>
> また、以下のテストケースも実装しました：
> TodoItem コンポーネント：
>
> - タスクのテキストが正しく表示される
> - チェックボックスをクリックすると onToggle が呼ばれる
> - 削除ボタンをクリックすると onDelete が呼ばれる
> - 完了状態のタスクは取り消し線が表示される
>
> TodoList コンポーネント：
>
> - 新しいタスクを追加できる
> - タスクを削除できる
> - タスクの完了状態を切り替えられる
> - 空のタスクは追加できない
>
> すべてのテストが正常に通過していることを確認できました。
> アプリケーションを使用するには、以下の機能が利用可能です：
>
> - 入力フィールドにタスクを入力し、「追加」ボタンをクリックするか、Enter キーを押してタスクを追加
> - チェックボックスをクリックしてタスクの完了/未完了を切り替え
> - 「削除」ボタンをクリックしてタスクを削除
>
> データはすべて localStorage に保存されるため、ブラウザを閉じても情報は保持されます。

:::

### 完成したソースコード

下記のリポジトリに完成したソースコードがあります。

https://github.com/tatsuyasusukida/openhands-todo-app-ts

### 動作確認

OpenHands ではなくローカルのターミナルで下記のコマンドを実行し、開発サーバーを起動します。

```sh:コマンド（例）
cd $HOME/OpenHands/todo-app # プロジェクト名が todo-app の場合
npm install
npm run dev
```

http://localhost:5173/ にアクセスして ToDo アプリが表示されることを確認します。

![ToDo アプリの動作確認](/images/articles/try-openhands-for-5-dollars/check-01.png)
_ToDo アプリの動作確認_

### テストの実行

OpenHands ではなくローカルのターミナルで下記のコマンドを実行し、テストを実行します。

```sh:コマンド（例）
cd $HOME/OpenHands/todo-app # プロジェクト名が todo-app の場合
npm test
```

```txt:コンソール出力（例）
> todo-app@0.0.0 test
> vitest run

 RUN  v0.34.6 /Users/susukida/OpenHands/todo-app

 ✓ src/__tests__/TodoItem.test.tsx (4)
 ✓ src/__tests__/TodoList.test.tsx (4)

 Test Files  2 passed (2)
      Tests  8 passed (8)
   Start at  06:29:52
   Duration  395ms (transform 35ms, setup 60ms, collect 126ms, tests 79ms, environment 265ms, prepare 69ms)
```

### トラブルシューティング

下記のスクラップに（僕の）試行錯誤の過程が記録されていますので、何かうまくいかないときに参考にしていただければ幸いです。

https://zenn.dev/tatsuyasusukida/scraps/2d8dd817ab8397

## 使ってみた感想

### 生成 AI との比較

ChatGPT や Claude などの生成 AI はコードやコマンドを生成してくれるので、これだけでもとても便利ですが、OpenHands はそれに加えてコードやコマンドを実行して問題があったら修正してくれるので、人間側でのコピペと試行錯誤を減らせる点で優れていると感じました。

一方、テストコードの追加などの簡単な作業の場合は [GitHub Copilot](https://github.com/features/copilot) や [Cody](https://sourcegraph.com/cody) などにチャットからお願いする方が操作が簡単なので、状況によって使い分けると良いかもしれません。

### 使うためのコツ

個人的に感じたのは下記の 3 点です。

- ローカルのファイルシステムをマウントするのは必須でやった方が良さそう。
- `npm run dev` や `vitest` のように終了しないコマンドを入力すると時間がかかるので避けた方が良さそう。
- なるべく 1 つのプロンプトでたくさんの作業をお願いする方がコストが安いのかも知れない。

公式ドキュメントの Prompting Best Practices がとても参考になるので下記に引用して要約します。

https://docs.all-hands.dev/modules/usage/prompting/prompting-best-practices

:::details Prompting Best Practices の要約

良いプロンプトの特徴を下記に示します。

- 具体的：追加すべき機能や修正すべきエラーを明確に説明する。
- 場所を特定：修正箇所がわかっている場合、コードベース内の具体的な場所を示す。
- 適切なスコープ：1 つの機能単位であり、通常 100 行程度を超えない規模に収める。

良いプロンプト例を下記に示します。

- utils/math_operations.py に calculate_average 関数を追加してください。この関数は数値リストを入力として受け取り、その平均を返します。
- frontend/src/components/UserProfile.tsx の 42 行目で発生している TypeError を修正してください。エラーは未定義のプロパティにアクセスしようとしていることが原因です。
- frontend/src/components/RegistrationForm.tsx の登録フォームのメールフィールドに入力検証を実装してください。送信前にメールが有効な形式であるかを確認するようにしてください。

悪いプロンプト例を下記に示します。

- コードをもっと良くしてください。（曖昧で具体性がない）
- バックエンド全体を別のフレームワークに書き換えてください。（スコープが広すぎる）
- ユーザー認証のどこかにバグがあります。見つけて修正してください。（具体性や修正箇所の情報が不足）

効果的なプロンプト作成のコツを下記に示します。

- 具体的に：期待する結果や解決すべき問題をできるだけ詳細に記述する。
- 文脈を提供：関連するファイルパスや行番号を含める。
- タスクを分割：大きな作業は小さく分割して提示する。
- エラーメッセージを含める：関連するエラーログやメッセージを添える。
- 言語やフレームワークを明記：文脈から自明でない場合は指定する。

:::

### 利用コストについて

スクラップを作成しならが 2 時間ほど遊んだ場合に 0.75 ドルの Anthropic API 利用料が発生しました。スクラップに書き込みながらなので倍くらい時間がかかっていますが、普通に使っている分には 1 時間で 10 ドルいくことはなさそうです。仮に 1 時間で 5 ドルとして 1 日に 6 時間使い、1 カ月に 20 日稼働した場合は 600 ドルになります。こうなると Devin は月額 500 ドルなので、Devin を使う方がコスト的には有利かもしれません。

Anthropic API 利用料の内訳を見ると Input Tokens が 0.00058 MTok、Input Tokens (Cache Hit) が 1.11 MTok となっていました。Input Tokens の単価は MTok あたり 3 ドルで Input Tokens (Cache Hit) の 10 倍なので、あまりキャッシュヒットしない場合はさらにコストがかかることが予想されます。

## おわりに

思い起こせば GitHub Copilot に脱帽し、その後は ChatGPT に驚愕し、そして今回は OpenHands に度肝を抜かれました。これからも AI がどんどん進化し、個人や小規模なチームでもできることが増えていくのが楽しみでなりません。AI により良いコードを生成してもらうためにも、新しい技術や知識の学習やプロンプトエンジニアリングなどのスキル向上に努めていきたいと思います。
