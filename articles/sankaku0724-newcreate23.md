---
title: "【初学者向け】Docker Composeを使ってみた"
emoji: "💫"
type: "tech"
topics:
  - "dockerdesktop"
  - "yaml"
  - "docker"
  - "dockercompose"
  - "cloud"
published: false
---

## はじめに

今回は、私の体験を基に，Docker Composeについて説明します。

:::message
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- Docker Desktop 4.32.0 (157355)
- Docker Engine 27.0.3
- Docker Compose v2.28.1-desktop.1
- MacOS Sonoma 14.5

## Dockerコマンド一覧

Dockerのコマンド一覧は、以下のサイトに記載されています。このサイトは、Docker公式ドキュメントを有志の方々が日本語に翻訳してくれているものです。

https://docs.docker.jp/engine/reference/commandline/index.html

公式による最新のドキュメントを確認したい人は、以下のリンクから飛ぶことができます。

https://docs.docker.com/

## Docker Composeとは？

[**Docker Compose**](https://docs.docker.jp/compose/index.html)は、複数のDockerコンテナを効率的に管理・実行するためのツールです。

Docker Composeを使用すると、**複数のコンテナを1つのYAMLファイルで定義し、単一のコマンドで一括して起動・停止できます.**
これにより，**複数コンテナの一括管理ができます．**
また，コンテナの設定やネットワーク、ボリュームなどをYAMLファイルで宣言的に記述できるため、複雑な環境でも設定を簡潔に管理でき，サービスの複製や拡張も容易になります.

Docker Composeのコマンド一覧は，以下のリンクから参照できます．

https://docs.docker.jp/v1.12/compose/reference/overview.html#docker-compose

ちなみに，[**Docker Desktopをインストールすると、Docker Composeも自動的にインストールされます。**](https://docs.docker.jp/compose/install/compose-desktop.html#docker-desktop-compose)
私の環境で以下のコマンドを実行し，バージョン確認をしてみます．

```
docker compose version
```

![](/images/sankaku23/1.png)
*別でインストールする必要がない*

Docker Desktopって便利ですね〜！

## Docker Composeを使ってみる

実際にDocker Composeを使って複数のコンテナを作成してみます．

### 1. 作業用ディレクトリを作成する

まず，「compose-test」という名前の作業用ディレクトリを作り，そこに移動します．

```
mkdir compose-test
cd compose-test
```

![](/images/sankaku23/2.png)

### 2. Dockerネットワークを作成する

移動したら，以下のコマンドで「mynetwork」という名前のDockerネットワークを作成します．

```
docker network create mynetwork
```

![](/images/sankaku23/3.png)
*Dockerネットワークを新規作成*

`docker network ls`コマンドで確認すると、「mynetwork」が作成されていることが確認できました。

```
docker network ls
```

![](/images/sankaku23/4.png)
*「mynetwork」が存在している*

### 3. `docker-compose.yml`を作成する

以下のような`docker-compose.yml`を作成してみます．
これはMySQLデータベースを使用したWordPress環境をセットアップするための記述です．

```yml:docker-compose.yml
version: '3.8'

services:
  wordpress-db:
    image: mysql:5.7
    platform: linux/amd64
    networks:
      - mynetwork
    volumes:
      - wordpress_db_volume:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: myrootpassword
      MYSQL_DATABASE: wordpressdb
      MYSQL_USER: wordpressuser
      MYSQL_PASSWORD: wordpresspass

  wordpress-app:
    depends_on:
      - wordpress-db
    image: wordpress
    networks:
      - mynetwork
    ports:
      - "8080:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: wordpress-db
      WORDPRESS_DB_NAME: wordpressdb
      WORDPRESS_DB_USER: wordpressuser
      WORDPRESS_DB_PASSWORD: wordpresspass

networks:
  mynetwork:

volumes:
  wordpress_db_volume:
```

:::details 作成したdocker-compose.ymlの詳細
1. WordPressデータベースサービス (`wordpress-db`):

- `MySQL 5.7`のイメージを使用しています。
- `platform`を`linux/amd64`に設定しています。
- `mynetwork`ネットワークに接続しています。
- データベースデータは`wordpress_db_volume`という名前のボリュームに保存され、永続化されます。
- コンテナが常に再起動するように設定しています。
- 環境変数で、MySQLのルートパスワード、データベース名、ユーザー名、パスワードを指定しています。

2. WordPressアプリケーションサービス (`wordpress-app`):

- `wordpress-db`サービスに依存しています（つまり、`wordpress-appはwordpress-db`が起動するまで待機します）。
- WordPressの公式イメージを使用しています。
- `mynetwork`ネットワークに接続しています。
- ホストのポート8080をコンテナのポート80にマッピングしており、ブラウザで[http://localhost:8080](http://localhost:8080/)にアクセスすることでWordPressに接続できるようになっています。
- 常に再起動するように設定されています。
- 環境変数で、WordPressが使用するデータベースのホスト、名前、ユーザー名、パスワードを指定しています。

3. ネットワーク (`mynetwork`):
- ここで定義しているネットワークを介して、サービス同士が通信できるようになります。

4. ボリューム (`wordpress_db_volume`):
- MySQLデータベースのデータを永続化するためのボリュームを定義しています。
:::

### 4. Docker Composeでコンテナを稼働させる

upコマンドでコンテナの作成と開始を行います．また，-dで端末から切り離してバックグラウンドで実行させます．

```
docker compose up -d
```

![](/images/sankaku23/5.png)
*実行画面*

ここで「'version' is obsolete」という警告が出ていますが，どうやら[最新のDocker環境において，versionプロパティは非推奨](https://qiita.com/mai_llj/items/a91bb375af68a1c88469#version%E3%83%97%E3%83%AD%E3%83%91%E3%83%86%E3%82%A3%E3%81%AF%E9%9D%9E%E6%8E%A8%E5%A5%A8)らしいです．ビルド自体には問題ないそうなので，今回はこのまま作業を続行します．

### 5. Docker Composeで何ができたのか確認する

以下のように，[`docker compose ps`](https://docs.docker.jp/compose/reference/ps.html)でDocker Composeに関連するコンテナの一覧を表示してみると，二つのコンテナが稼働していることが確認できました．

```
docker compose ps
```

![](/images/sankaku23/6.png)
*二つのコンテナが稼働している*

`docker ps`でも確認してみます．

```
docker ps
```

![](/images/sankaku23/7.png)
*二つのコンテナが稼働していることが確認できた*

このように，**Docker Composeで稼動させたコンテナはDocker Composeとは関係ない形で稼動させたコンテナと遜色なく扱うことができる**ことがわかりました．

次は，ネットワークについて確認してみます．

```
docker network ls
```

![](/images/sankaku23/8.png)
*Docker Composeで作成したネットワークが存在している*



![](/images/sankaku23/9.png)

![](/images/sankaku23/10.png)

![](/images/sankaku23/11.png)

![](/images/sankaku23/12.png)

![](/images/sankaku23/13.png)

![](/images/sankaku23/14.png)

![](/images/sankaku23/15.png)

![](/images/sankaku23/16.png)

![](/images/sankaku23/17.png)

![](/images/sankaku23/18.png)

![](/images/sankaku23/19.png)

![](/images/sankaku23/20.png)

![](/images/sankaku23/21.png)

![](/images/sankaku23/22.png)

![](/images/sankaku23/23.png)

![](/images/sankaku23/24.png)

![](/images/sankaku23/25.png)

![](/images/sankaku23/26.png)

![](/images/sankaku23/27.png)

![](/images/sankaku23/28.png)

![](/images/sankaku23/29.png)

![](/images/sankaku23/30.png)

![](/images/sankaku23/31.png)

![](/images/sankaku23/32.png)

![](/images/sankaku23/33.png)

![](/images/sankaku23/34.png)


## さいごに

ここまで記事を読んでくださり、ありがとうございました！

今回は、Docker Composeについて紹介しました。私もまだまだ慣れていないため，今後も知識を養いながら，色々なことにチャレンジしてみたいです．

**皆さんも素敵なハッピーDockerライフを！！！🌸**
