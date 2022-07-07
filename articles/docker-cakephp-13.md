---
title: "11年前にCakePHPで作ったアプリをDockerで延命する"
emoji: "🍰"
type: "tech"
topics: ["docker", "php", "cakephp"]
published: true
---

## この記事について

この記事では2011年6月20日に[CakePHP](https://cakephp.org/)（当時のバージョンは1.3.9）で作った複式簿記アプリをDockerで延命した手順について記録します。



## おおまかな流れ

おおまかな流れを下記に示します。

1. コーディングの準備
2. ソースコードの作成
3. ソースコードの変更
4. コンテナイメージのビルド
5. コンテナの起動
6. 動作確認
7. コンテナの停止



## コーディングの準備

コーディングの準備をします。

```shell
touch 000-default.conf Dockerfile
```



## ソースコードの作成

エディタで下記のファイルを開いて内容を入力します。

### 000-default.conf

```
<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined

  <Directory /var/www/html>
    AllowOverride All
  </Directory>
</VirtualHost>
```

### Dockerfile

```dockerfile
FROM php:5.6-apache
RUN a2enmod rewrite
COPY 000-default.conf /etc/apache2/sites-available/000-default.conf
COPY . /var/www/html
```



## ソースコードの変更

エディタで下記のファイルを開いて内容を変更します。

### app/config/database.php

```diff
@@ -7,8 +7,8 @@ class DATABASE_CONFIG {
                'host' => '',
                'login' => '',
                'password' => '',
-               'database' => 'C:\\Users\\susukida\\Dropbox\\data\\kozukai.sqlite',
+               'database' => '/data/kozukai.sqlite',
                'encoding' => 'utf8'
        );
 }
```

### app/webroot/index.php

```diff
@@ -50,7 +50,7 @@
  *
  */
        if (!defined('CAKE_CORE_INCLUDE_PATH')) {
-               define('CAKE_CORE_INCLUDE_PATH', 'C:' . DS . 'xampp' . DS . 'htdocs' . DS . 'kozukai');
+               define('CAKE_CORE_INCLUDE_PATH', ROOT);
        }
 
 /**
```



## コンテナイメージのビルド

ターミナルで下記のコマンドを実行してコンテナイメージを作成します。

```shell
docker image build -t kozukai:latest .
```



## コンテナの起動

```shell
docker container run -d \
  --rm \
  -p 3000:80 \
  --name kozukai \
  -v /Users/susukida/Dropbox/data:/data \
  kozukai:latest
```



## 動作確認

ブラウザで http://localhost:3000/ にアクセスします。

### 参考画像

![](/images/articles/docker-cakephp-13/img-check-01.jpg)

![](/images/articles/docker-cakephp-13/img-check-02.jpg)

![](/images/articles/docker-cakephp-13/img-check-03.jpg)

![](/images/articles/docker-cakephp-13/img-check-04.jpg)

![](/images/articles/docker-cakephp-13/img-check-05.jpg)



## コンテナの停止

```shell
docker container stop kozukai
```



## おわりに

11年前の2011年6月1日にフリーランスのプログラマーになった頃、会計ソフトを買うお金も惜しくて当時人気だったCakePHPを使って複式簿記アプリを作成しましたが、まさか10年以上使い続けるとは思っていませんでした。さすがに今では市販の会計ソフトを使っていますが、個人の家計簿ではいまだにこの複式簿記アプリを使っています。

新しいPCを買った時にあまり使わないPHPを動かすために[XAMPP](https://www.apachefriends.org/)をインストールするのが嫌だったので必要な時だけDockerで動かせるようにしました。新しいアプリに移行するまではDockerで延命し続けようと思います。
