---
title: "FlutterでFerryを使ってGraphQL APIにアクセスする方法"
emoji: "🕳"
type: "tech"
topics: ["flutter", "graphql", "ferry"]
published: true
---

## この記事について

FlutterのGraphQLクライアントである[Ferry](https://ferrygraphql.com/)を使ってGraphQL APIにアクセスする方法について紹介します。内容については[Ferry公式ドキュメント](https://ferrygraphql.com/docs/)＋[Ferry GitHubページの公式サンプル](https://github.com/gql-dart/ferry/tree/master/examples)をベースに簡略化しています。



## コーディングの準備

ターミナルで下記のコマンドを実行してFlutterプロジェクトを作成します。VSCodeを使っている場合は`Command + Shift + P`を押してからFlutter: New Projectを実行してもOKです。

```sh
flutter create hello_ferry
```

続いてターミナルで下記のコマンドを実行してディレクトリとファイルを作成します。

```sh
cd hello_ferry
mkdir -p lib/src/components/graphql
touch build.yaml
touch lib/src/components/pokemon_card.dart
touch lib/src/components/pokemon_list.dart
touch lib/src/components/graphql/all_pokemon.graphql
touch lib/src/components/graphql/pokemon_card_fragment.graphql
```

続いてターミナルで下記のコマンドを実行して必要なパッケージをインストールします。

```sh
flutter pub add ferry ferry_flutter gql_http_link built_collection
flutter pub add --dev ferry_generator build_runner
flutter pub get
```



## GraphQLスキーマのダウンロード

ターミナルで下記のコマンドを実行してget-graphql-schemaをインストールします。

```sh
npm install -g get-graphql-schema
```

続いてターミナルで下記のコマンドを実行してGraphQLスキーマをダウンロードします。

```sh
get-graphql-schema https://pokeapi.dev > lib/schema.graphql
```

ダウンロードが成功するとlib/schema.graphqlに下記のファイルが作成されます。

```graphql:lib/schema.graphql
directive @cacheControl(maxAge: Int, scope: CacheControlScope) on FIELD_DEFINITION | OBJECT | INTERFACE

type Ability {
  id: String
  name: String
  slot: Int
  is_hidden: Boolean
  is_main_series: Boolean
  pokemons: [Int]
}

# 以下、省略
```



## GraphQLクラスの自動生成

エディタでlib/src/components/graphql/all_pokemon.graphqlとlib/src/components/graphql/pokemon_card_fragment.graphqlを開いて下記のGraphQLクエリの内容を入力します。

@[gist](https://gist.github.com/tatsuyasusukida/520b3e9de3d42421cefa0f803ffd3c99?file=all_pokemon.graphql)

@[gist](https://gist.github.com/tatsuyasusukida/520b3e9de3d42421cefa0f803ffd3c99?file=pokemon_card_fragment.graphql)

続いてエディタでbuild.yamlを開いて下記の内容を入力します。パッケージ名が`hello_ferry`ではない場合やスキーマファイルのパスが`lib/schema.graphql`ではない場合は適宜編集します。

@[gist](https://gist.github.com/tatsuyasusukida/520b3e9de3d42421cefa0f803ffd3c99?file=build.yaml)

ターミナルで下記のコマンドを実行してGraphQLクラスを自動生成します。

```sh
flutter pub run build_runner build
```

自動生成が成功するとターミナルに下記のメッセージが表示されます。

```
[INFO] Succeeded after 13.8s with 27 outputs (40 actions)
```

自動生成されるファイルは下記の通りです。

- lib/__generated__/schema.schema.gql.dart
- lib/__generated__/schema.ast.gql.dart
- lib/__generated__/schema.schema.gql.g.dart
- lib/__generated__/serializers.gql.dart
- lib/__generated__/serializers.gql.g.dart
- lib/src/components/graphql/__generated__/all_pokemon.ast.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.data.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.data.gql.g.dart
- lib/src/components/graphql/__generated__/all_pokemon.req.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.req.gql.g.dart
- lib/src/components/graphql/__generated__/all_pokemon.var.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.var.gql.g.dart
- lib/src/components/graphql/__generated__/pokemon_card_fragment.ast.gql.dart
- lib/src/components/graphql/__generated__/pokemon_card_fragment.data.gql.dart
- lib/src/components/graphql/__generated__/pokemon_card_fragment.data.gql.g.dart
- lib/src/components/graphql/__generated__/pokemon_card_fragment.req.gql.dart
- lib/src/components/graphql/__generated__/pokemon_card_fragment.req.gql.g.dart
- lib/src/components/graphql/__generated__/pokemon_card_fragment.var.gql.dart
- lib/src/components/graphql/__generated__/pokemon_card_fragment.var.gql.g.dart



## パーミッションの設定

macOSの場合はmacos/Runner/DebugProfile.entitlementsとmacos/Runner/Release.entitlementsに下記の内容を追加します。

```xml
<key>com.apple.security.network.client</key>
<true/>
```

ちなみにDebugProfile.entitlementsには`com.apple.security.network.server`というよく似たキーがありますがこれを消すとFlutterアプリが起動しなくなるのでご注意ください（私は15分くらいハマりました）。

AndroidとiOSについて未検証ですがFlutter公式ドキュメントの下記のページが参考になりそうです。

https://docs.flutter.dev/development/data-and-backend/networking

https://docs.flutter.dev/release/breaking-changes/network-policy-ios-android



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### lib/main.dart

@[gist](https://gist.github.com/tatsuyasusukida/520b3e9de3d42421cefa0f803ffd3c99?file=main.dart)

### lib/src/components/pokemon_list.dart

@[gist](https://gist.github.com/tatsuyasusukida/520b3e9de3d42421cefa0f803ffd3c99?file=pokemon_list.dart)

### lib/src/components/pokemon_card.dart

@[gist](https://gist.github.com/tatsuyasusukida/520b3e9de3d42421cefa0f803ffd3c99?file=pokemon_card.dart)



## 動作確認

ターミナルで下記のコマンドを実行してFlutterアプリを起動します。VSCodeを使っている場合は`F5`を押すかDebug: Start Debuggingを実行してもOKです。

```
flutter run
```

Flutterアプリが起動してポケモンの名前一覧が表示されることを確認します。

![](/images/articles/how-to-access-graphql-api-with-ferry-in-flutter/check-01.png)



## おわりに

Ferryの公式ドキュメントは簡潔で読みやすくて良いのですが説明が若干不足しているように感じる部分が多く、入門するにあたりハマってしまうことが多かったです。最後になりますが私が時間を浪費してしまったポイント等について紹介したいと思います。

### インストールするパッケージ

[Setup](https://ferrygraphql.com/docs/setup)のページでは下記のパッケージをインストールするように説明されています。

```yaml:pubspec.yaml
dependencies:
  ferry: #[latest-version]
  gql_http_link: #[latest-version]

dev_dependencies:
  ferry_generator: #[latest-version]
  build_runner: #[latest-version]
```

上記に加えてdependenciesとして下記2つのパッケージをインストールするのがおすすめです。

- ferry_flutter: FlutterでFerryを使うのを便利にするパッケージ
- built_collection: VSCodeでBuiltListの自動インポートができるようになる

https://ferrygraphql.com/docs/flutter

### GrqphQLエンドポイントURL

[Generate GraphQL Classes](https://ferrygraphql.com/docs/codegen#how-it-works)のページでは下記のGrqphQLクエリが記載されています。

```graphql:all_pokemon.graphql
query AllPokemon($first: Int!) {
  pokemons(first: $first) {
    id
    name
    maxHP
    image
  }
}
```

上記のクエリは恐らく https://graphql-pokemon2.vercel.app を対象にしているものと思われます。一方、公式サンプルでは https://pokeapi.dev を対象にしています。結論としてはどちらを使っても良いのですが入門時はどちらを使えば良いか悩みました。

https://graphql-pokemon2.vercel.app を使うメリット／デメリットは下記の通りです。

- メリット：上記のクエリがそのまま使える、ポケモンの画像を表示できる（後述）
- デメリット：ソースコードの大幅な改変が必要

https://pokeapi.dev を使うメリット／デメリットは下記の通りです。

- メリット：ソースコードの改変がほぼ不要
- デメリット：上記のクエリがそのまま使えない（代わりに公式サンプルのクエリが使えば良い）、ポケモンの画像を表示できない（後述）

動作確認の時にポケモンの画像を表示できないのは残念ですが https://pokeapi.dev を使った方が簡単です。ちなみに https://graphql-pokemon2.vercel.app を使うとGraphQLクラスを自動生成する時にschema.schema.gql.dartなどの一部のファイルが出力され無くなります、理由は不明です。入門するにあたりこのファイルは必要ないのですが自分のやり方が間違っているのではないかという不安に駆られて色々と調べるのに時間を費やしてしまいました。

### クライアントの初期化

[Setup](https://ferrygraphql.com/docs/setup#initialize-the-client)のページではクライアントの初期化の手順が下記のように紹介されています。

```dart
import 'package:gql_http_link/gql_http_link.dart';
import 'package:ferry/ferry.dart';
import 'package:<your_pkg>/<path_to_your_schema>__generated__/schema.schema.gql.dart' show possibleTypesMap;

final link = HttpLink("[path/to/endpoint]");
final cache = Cache(possibleTypes: possibleTypesMap);
final client = Client(link: link, cache: cache);
```

`possibleTypesMap`が`{}`である場合、コンパイルが通らないので下記の修正する必要があります。

```dart
final cache = Cache();
```

[Configuring the cache](https://ferrygraphql.com/docs/cache-configuration#passing-the-possibletypes-map)のページでは`possibleTypes`について下記のように説明されています。

> When the schema contains unions, interfaces queries use fragments, the cache needs a little help to correctly normalize/denormalize data. In this case, pass the possibleTypes parameter to the cache constructor

上記の英文はよくわからないのですが恐らくunion、interface、フラグメントを使うクエリを使っている場合は`Cache`のコンストラクターに`possibleTypes`を指定してください、といった意味なのではないかと思います。逆にこれらを使っていない場合は`possibleTypes`の指定が不要なのではないかと思います。

### ネットワークのパーミッション

パーミッションの設定で紹介したようにmacOSやAndroidではHTTPリクエストを送信するのに設定が必要になります。多分iOSも必要なのではないかと思います。iOSやAndroidのアプリ開発の知識がある人にとっては当然のことなので割愛されたのかも知れませんがSetupのページに1行でも良いので「Ferryを利用するには適切なパーミッション設定が必要です」と書いておいてくれれば良いのにと思います。

### ポケモンの画像が表示されない

GraphQLエンドポイントURLとして https://pokeapi.dev を使うとポケモン画像のURL（Pokemonタイプのavatarフィールド）として https://pokeres.bastionbot.org/images/pokemon/1.png のような結果が得られますが、このURLにアクセスしてもポケモンの画像は表示されません。Google Chromeなどのブラウザからアクセスしても表示されないので恐らくサーバー側の問題だと思うのですが、自分の方が何かを間違えているのではないかという不安に駆られて色々と調べるのに時間を費やしてしまいました。
