---
title: "【初学者向け】Docker Composeの基礎と実践"
emoji: "💫"
type: "tech"
topics:
  - "dockerdesktop"
  - "yaml"
  - "docker"
  - "dockercompose"
  - "cloud"
published: true
---

## はじめに

今回は私の体験を基に、**Docker Compose**について説明します。

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

**[Docker Compose](https://docs.docker.jp/compose/index.html)は、複数のDockerコンテナを効率的に管理・実行するためのツールです。**

Docker Composeを使用すると、**複数のコンテナを1つのYAMLファイルで定義し、単一のコマンドで一括して起動・停止できます。**
これにより、**複数コンテナの一括管理ができます。**
また、コンテナの設定やボリュームなどをYAMLファイルで宣言的に記述できるため、複雑な環境でも設定を簡潔に管理でき、サービスの複製や拡張も容易になります.

Docker Composeのコマンド一覧は、以下のリンクから参照できます。

https://docs.docker.jp/v1.12/compose/reference/overview.html#docker-compose

ちなみに、[**Docker Desktopをインストールすると、Docker Composeも自動的にインストールされます。**](https://docs.docker.jp/compose/install/compose-desktop.html#docker-desktop-compose)

既にDocker Desktopがインストールしてある私の環境で以下のコマンドを実行し、バージョン確認をしてみます。

```
docker compose version
```

![](/images/sankaku23/1.png)
*別でインストールする必要がない*

Docker Desktopって便利ですね〜！

## Docker Composeを使ってみる

実際にDocker Composeを使って複数のコンテナを作成してみます。

### 1. 作業用ディレクトリを作成する

まず、「compose-test」という名前の作業用ディレクトリを作り、そこに移動します。

```
mkdir compose-test
cd compose-test
```

![](/images/sankaku23/2.png)

### 2. Dockerネットワークを作成する

移動したら、以下のコマンドで「mynetwork」という名前のDockerネットワークを作成します。

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

### 3. 「docker-compose.yml」を作成する

以下のような「docker-compose.yml」を作成してみます。
これはMySQLデータベースを使用したWordPress環境をセットアップするための記述です。

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
1. WordPressデータベースサービス (**`wordpress-db`**):

- `MySQL 5.7`のイメージを使用しています。
- `platform`を`linux/amd64`に設定しています。
- `mynetwork`ネットワークに接続しています。
- データベースデータは`wordpress_db_volume`という名前のボリュームに保存され、永続化されます。
- コンテナが常に再起動するように設定しています。
- 環境変数で、MySQLのルートパスワード、データベース名、ユーザー名、パスワードを指定しています。

2. WordPressアプリケーションサービス (**`wordpress-app`**):

- `wordpress-db`サービスに依存しています（つまり、`wordpress-appはwordpress-db`が起動するまで待機します）。
- WordPressの公式イメージを使用しています。
- `mynetwork`ネットワークに接続しています。
- ホストのポート8080をコンテナのポート80にマッピングしており、ブラウザで[http://localhost:8080](http://localhost:8080/)にアクセスすることでWordPressに接続できるようになっています。
- 常に再起動するように設定されています。
- 環境変数で、WordPressが使用するデータベースのホスト、名前、ユーザー名、パスワードを指定しています。

3. ネットワーク (**`mynetwork`**):
- ここで定義しているネットワークを介して、サービス同士が通信できるようになります。

4. ボリューム (**`wordpress_db_volume`**):
- MySQLデータベースのデータを永続化するためのボリュームを定義しています。
:::

### 4. Docker Composeでコンテナを稼働させる

[**`docker compose up`コマンド**](https://docs.docker.jp/engine/reference/commandline/compose_up.html)でコンテナの作成と開始を行います。
また、-dで端末から切り離してバックグラウンドで実行させます。

```
docker compose up -d
```

![](/images/sankaku23/5.png)
*実行画面*

ここで「**`'version' is obsolete`**」という警告が出ていますが、どうやら[最新のDocker環境において、versionプロパティは非推奨](https://qiita.com/mai_llj/items/a91bb375af68a1c88469#version%E3%83%97%E3%83%AD%E3%83%91%E3%83%86%E3%82%A3%E3%81%AF%E9%9D%9E%E6%8E%A8%E5%A5%A8)らしいです。ビルド自体には問題ないそうなので、今回はこのまま続行します。

### 5. Docker Composeで何ができたのか確認する

以下のように、[`docker compose ps`コマンド](https://docs.docker.jp/compose/reference/ps.html)でDocker Composeに関連するコンテナの一覧を表示してみると、二つのコンテナが稼働していることが確認できました。

```
docker compose ps
```

![](/images/sankaku23/6.png)
*二つのコンテナが稼働している*

**Docker Composeで起動したコンテナ名は、「作業用ディレクトリ名_コンテナ名_1」というような命名規則になります。**

`docker ps`コマンドでも確認してみます。

```
docker ps
```

![](/images/sankaku23/7.png)
*二つのコンテナが稼働していることが確認できた*

次は、ネットワークについて確認してみます。

```
docker network ls
```

![](/images/sankaku23/8.png)
*Docker Composeで作成したネットワークが存在している*

実行結果から、**Docker Composeではネットワークも作成することができる**ということがわかりました。
[別にネットワークを作成する](https://zenn.dev/joho0724/articles/sankaku0724-newcreate23#2.-docker%E3%83%8D%E3%83%83%E3%83%88%E3%83%AF%E3%83%BC%E3%82%AF%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B)必要はなかったということですね。

また、ボリュームについても確認してみます。

```
docker volume ls
```

![](/images/sankaku23/9.png)
*二つのボリュームが確認できた*

このように、**Docker Composeで稼動させたコンテナはDocker Composeとは関係ない形で稼動させたコンテナと遜色なく扱うことができる**ことがわかりました。

### 6. WordPressの動作確認をする

コンテナが稼動しましたが、本当にしっかり稼動できているのか確認するためにWordPressの動作確認をしてみます。

:::message
今回は、Docker Composeがメインなので、WordPressの詳細については説明しないことにします。
:::

ブラウザで[http://localhost:8080](http://localhost:8080/)にアクセスすると以下のような画面が表示されました。

![](/images/sankaku23/10.png)

「日本語」を選択し、「次へ」をクリックして進みます。

![](/images/sankaku23/11.png)

進んだら、サイトのタイトルやユーザー名、パスワードなどの必要事項を入力します。

:::message
ここで指定したユーザー名やパスワードは、管理者ページにログインする際に必要となるので忘れないようにしましょう。
:::

入力したら、WordPressのインストールに成功した旨の画面が表示されます。「ログイン」をクリックして進みます。

![](/images/sankaku23/12.png)

進んだら、ユーザー名やパスワードを入力してログインします。

![](/images/sankaku23/13.png)

ログインすると、管理者画面が表示されます。

![](/images/sankaku23/14.png)

上記の作業により、WordPressを扱えるようになりました。

![](/images/sankaku23/15.png)
*サイトが閲覧できる*

これで、Docker Composeで作成したコンテナが正常に動作していることが確認できました。

### 7. Docker Composeでコンテナを停止させ、破棄する

では、起動したコンテナー式を[**`docker compose down`コマンド**](https://docs.docker.jp/compose/reference/down.html)で停止させ、破棄してみます。
**`docker compose down`コマンドは、コンテナやネットワークを停止するだけでなく、それらを破棄するコマンドです。**
ただし**デフォルトでは、ボリュームは削除しない**ようになっています。もし、ボリュームが削除された場合、データが永続化されなくなるからです。

```
docker compose down
```

![](/images/sankaku23/16.png)

`docker compose ps`で確認すると、コンテナが存在していないことが確認できました。

```
docker compose ps
```

![](/images/sankaku23/17.png)

`docker ps -a`でも同様です。

![](/images/sankaku23/18.png)

次は、ネットワークについて確認してみます。

![](/images/sankaku23/19.png)
*「compose-test_mynetwork」が存在しない*

こちらもDocker Composeで作成したものが存在していないことが確認できました。

また、ボリュームについても確認してみます。

![](/images/sankaku23/20.png)

こちらは残っています。
そのため、もう一度、`docker compose up`を実行した場合、このボリュームの内容はそのまま使われるため、マウントしているMySQLコンテナが保存しているデータベースのデータが失われることはありません。

### 8. 一つずつコンテナを操作する

このように、`docker compose`コマンドではupとdownを指定することで、まとめて起動ならびに停止・破棄ができます。
しかし、一つずつコンテナを操作したいこともあるでしょう。
しかし、Docker Composeで管理されているものを、dockerコマンドで操作すると反故が生じる可能性もあります。（設定の不整合や依存関係の破損など）
こうした理由から、**Docker Composeで起動したものは、Docker Composeから操作すべきです。**

#### 8.1 Docker Composeで起動する

まず、以下のコマンドで全て起動します。

```
docker compose up -d
```

#### 8.2 コンテナの中身を確認する

起動したコンテナの内、wordpress-appのほうに入り込んでみます。
docker compose execを実行すると、任意のコマンドを実行できます。

```
docker compose exec wordpress-app /bin/bash
```

![](/images/sankaku23/21.png)

ここで、以下のコマンドを実行してみます、

```
ls /var/www/html
```

`ls /var/www/html`コマンドは、指定されたディレクトリ`/var/www/html`の内容をリスト表示します。このディレクトリは、通常、Webサーバーのデフォルトのドキュメントルートとして使用され、ウェブサイトのファイルが格納されています。

![](/images/sankaku23/22.png)
*様々なものが格納されている*

確認したので、`exit`で終了します。

![](/images/sankaku23/23.png)

#### 8.3 片方のコンテナだけを停止させる

ではここで、**片方のコンテナだけを停止**させてみます。
今回は、データベースの役割を持つ`wordpress-db`コンテナを停止させてみます。

```
docker compose stop wordpress-db
```

![](/images/sankaku23/24.png)

`docker compose ps -a`で確認すると、片方だけが停止していることが確認できました。

![](/images/sankaku23/25.png)

#### 8.4 再度、稼動させる

再度、稼動します。これには、二つの方法があります。

##### 8.4.1 docker compose start

一つ目の方法は、`docker compose start wordpress-db`を実行することです。
この場合、wordpress-dbだけが起動します。

![](/images/sankaku23/28.png)
*`docker compose ps -a`で確認すると、両方とも起動しているのが確認できた*

##### 8.4.2 docker compose up -d

二つ目の方法は、`docker compose up -d`を実行することです。

まず、`docker compose stop wordpress-db`で片方のコンテナだけを停止させます。

![](/images/sankaku23/30.png)

そして、`docker compose up -d`を実行します。

![](/images/sankaku23/31.png)

**この場合、「docker-compose.yml」の内容が再実行され、それに伴って`wordpress-db`も起動します。**

![](/images/sankaku23/32.png)
*`docker compose ps -a`で確認すると、両方とも起動しているのが確認できた*

### 9. 作業終了

`docker compose down`を実行します。

![](/images/sankaku23/33.png)

## さいごに

ざっくりまとめてみます。

1. **「docker-compose.yml」に記述する**
稼動させたいコンテナ、ネットワーク、ボリュームの情報を「docker-compose.yml」ファイルに記述します。

2. **`docker compose up`でまとめて起動する**
docker-compose.ymlファイルを置いた場所をカレントディレクトリにし、`docker-compose up`を実行すると、まとめて起動させることができます。

3. **`docker compose down`でまとめて停止・破棄する**
`docker compose down`を実行すると、まとめてコンテナが停止し、ネットワークと共に破棄されます。ただし、ボリュームは残ります。

ここまで記事を読んでくださり、ありがとうございました！

今回は、Docker Composeについて紹介しました。私もまだまだ慣れていないため、今後も知識を養いながら、色々なことにチャレンジしてみたいです。

**皆さんも素敵なハッピーDockerライフを！！！🌸**
