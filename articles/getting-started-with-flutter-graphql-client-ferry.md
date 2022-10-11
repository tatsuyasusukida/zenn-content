---
title: "FlutterのGraphQLクライアントFerry入門はハマり所が多い"
emoji: "🕳"
type: "tech"
topics: ["flutter", "graphql", "ferry"]
published: false
---

## この記事について

FlutterのGraphQLクライアントである[Ferry](https://ferrygraphql.com/)の公式ドキュメントを読みながら簡単なサンプルアプリを作ってみようとした所、ハマってしまうポイントが多くて少し苦労しました。この記事ではサンプルアプリ作成の手順を説明するとともにハマってしまったポイントについて紹介します。



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
touch lib/src/components/all_pokemon.dart
touch lib/src/components/graphql/all_pokemon.graphql
```

続いてターミナルで下記のコマンドを実行して必要なパッケージをインストールします。

```sh
flutter pub add ferry ferry_flutter gql_http_link built_collection
flutter pub add --dev ferry_generator build_runner
flutter pub get
```

### ハマりポイント

Flutterでferryを使う場合はferry_flutterパッケージをインストールした方が望ましいですが、ferry_flutterパッケージをインストールすることの説明はFerry公式ドキュメントの[Setup](https://ferrygraphql.com/docs/setup#install-dependencies)ページには無く、[Using with Flutter](https://ferrygraphql.com/docs/flutter#setup)ページにあります。

また、ハマりポイントという程でもないですがFerry公式ドキュメントの[Setup](https://ferrygraphql.com/docs/setup#install-dependencies)ページではbuilt_collectionをインストールする説明がありません。built_collectionはall_pokemon.dartで`BuiltList()`を使うために必要となりますがferryをインストールするとbuilt_valueがインストールされ、built_valueをインストールするとbuilt_collectionがインストールされるので明示的なインストールは不要です。しかしながら、built_cllectionを明示的にインストールした方がVSCodeの自動補完が効くようになるのでおすすめです。



## GraphQLスキーマのダウンロード

ターミナルで下記のコマンドを実行してget-graphql-schemaをインストールします。

```sh
npm install -g get-graphql-schema
```

続いてターミナルで下記のコマンドを実行してGraphQLスキーマをダウンロードします。

```sh
get-graphql-schema https://graphql-pokemon2.vercel.app > lib/schema.graphql
```

ダウンロードが成功するとlib/schema.graphqlに下記のファイルが作成されます。

```graphql:lib/schema.graphql
"""Represents a Pokémon's attack types"""
type Attack {
  """The name of this Pokémon attack"""
  name: String

  """The type of this Pokémon attack"""
  type: String

  """The damage of this Pokémon attack"""
  damage: Int
}

# 以下、省略
```

### ハマりポイント

Ferry公式ドキュメントの[Generate GraphQL Classes](https://ferrygraphql.com/docs/codegen#download-your-graphql-schema)ではGraphQLスキーマをダウンロードするコマンドが下記のように説明されています。

```sh
get-graphql-schema [ENDPOINT_URL] > lib/schema.graphql
```

`[ENDPOINT_URL]`の部分については初見ではFerryの[GitHubページ](https://github.com/gql-dart/ferry/blob/master/examples/pokemon_explorer/lib/main.dart)のexamples/pokemon_explorer/lib/main.dartのソースコードを参考にして`https://pokeapi.dev`にしたのですが、2つの理由から`https://graphql-pokemon2.vercel.app`の方が望ましいと思っています。その理由については後の手順で説明します。



## GraphQLクラスの自動生成

エディタでlib/src/components/graphql/all_pokemon.graphqlを開いて下記のGraphQLクエリの内容を入力します。このクエリの内容はFerry公式ドキュメントの[Generate GraphQL Classes](https://ferrygraphql.com/docs/codegen#how-it-works)ページで説明されている通りです。

```graphql:lib/src/components/graphql/all_pokemon.graphql
query AllPokemon($first: Int!) {
  pokemons(first: $first) {
    id
    name
    maxHP
    image
  }
}
```

続いてエディタでbuild.yamlを開いて下記の内容を入力します。パッケージ名が`hello_ferry`ではない場合やスキーマファイルのパスが`lib/schema.graphql`ではない場合は適宜編集します。

```build.yaml
targets:
  $default:
    builders:
      ferry_generator|graphql_builder:
        enabled: true
        options:
          schema: hello_ferry|lib/schema.graphql
      ferry_generator|serializer_builder:
        enabled: true
        options:
          schema: hello_ferry|lib/schema.graphql
```

ターミナルで下記のコマンドを実行してGraphQLクラスを自動生成します。

```sh
flutter pub run build_runner build
```

自動生成が成功するとターミナルに下記のメッセージが表示されます。

```
[INFO] Succeeded after 13.5s with 14 outputs (25 actions)
```

自動生成されるファイルは下記の通りです。

- lib/__generated__/schema.ast.gql.dart
- lib/__generated__/serializers.gql.dart
- lib/__generated__/serializers.gql.g.dart
- lib/src/components/graphql/__generated__/all_pokemon.ast.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.data.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.data.gql.g.dart
- lib/src/components/graphql/__generated__/all_pokemon.req.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.req.gql.g.dart
- lib/src/components/graphql/__generated__/all_pokemon.var.gql.dart
- lib/src/components/graphql/__generated__/all_pokemon.var.gql.g.dart

### ハマりポイント

後の手順でFerryクライアントを作成する時にlib/__generated__/schema.schema.gql.dartから`possibleTypesMap`をインポートするのですが上記の通りこのファイルは生成されていません。詳しいことは分かりませんがlib/schema.graphqlの内容によってlib/__generated__/schema.schema.gql.dartが生成される場合と生成されない場合があるようです。今回のサンプルアプリ作成では無くても困らないので無視して大丈夫です（そういう私は気になってしまって原因を調べるのに1時間くらい時間を溶かしてしまったのですが…）。

GrqphQLエンドポイントが`https://pokeapi.dev`の場合はAllPokemonクエリがスキーマに適合しないのでGraphQLクラスの自動生成が失敗します。これがエンドポイントが`https://graphql-pokemon2.vercel.app`の方が良い理由の1つ目です。ちなみに自動生成を成功させるにはlib/src/components/graphql/all_pokemon.graphqlに下記の内容を入力します。

```graphql:lib/src/components/graphql/all_pokemon.graphql
query AllPokemon($limit: Int!, $offset: Int!) {
  pokemons(limit: $limit, offset: $offset) {
    results {
      id
      name
      avatar
      height {
        in_meter
      }
      weight {
        in_kg
      }
    }
  }
}
```



## コーディング

エディタで下記のファイルを開いて内容を入力します。

### lib/main.dart

@[gist]()

### lib/src/components/pokemon_list.dart

@[gist]()

### lib/src/components/pokemon_card.dart

@[gist]()

### ハマりポイント

ソースコードの内容についてはFerryのGitHubページの[examples/pokemon_explorer](https://github.com/gql-dart/ferry/tree/master/examples/pokemon_explorer)を参考にして一部改変しました。GraphQLエンドポイントが`https://pokeapi.dev`の場合は変更がほぼ不要なのですが、`https://graphql-pokemon2.vercel.app`の場合は変更がかなり必要になります。

Ferry公式ドキュメントの[Setup](https://ferrygraphql.com/docs/setup#initialize-the-client)ページでは下記のようにクライアントを初期化する手順が説明されています。

```dart
import 'package:gql_http_link/gql_http_link.dart';
import 'package:ferry/ferry.dart';
import 'package:<your_pkg>/<path_to_your_schema>__generated__/schema.schema.gql.dart' show possibleTypesMap;

final link = HttpLink("[path/to/endpoint]");
final cache = Cache(possibleTypes: possibleTypesMap);
final client = Client(link: link, cache: cache);
```

`possibleTypes`については[Configuration](https://ferrygraphql.com/docs/cache-configuration#passing-the-possibletypes-map)ページに記載があります。

> When the schema contains unions, interfaces queries use fragments, the cache needs a little help to correctly normalize/denormalize data. In this case, pass the possibleTypes parameter to the cache constructor

上記の英文はよくわからないのですが恐らくunion、interface、フラグメントを使うクエリを使っている場合は`Cache`のコンストラクターに`possibleTypes`を指定してください、といった意味なのではないかと思います。逆にこれらを使っていない場合は`possibleTypes`の指定が不要なのではないかと思います。また、指定しようにもGraphQLエンドポイントが`https://graphql-pokemon2.vercel.app`の場合はそもそもlib/__generated__/schema.schema.gql.dartが生成されないので指定できません。



## パーミッションの設定

macos/Runner/DebugProfile.entitlements

macos/Runner/Release.entitlements


