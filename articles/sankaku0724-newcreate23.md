---
title: "【初学者向け】Docker Composeの基礎"
emoji: "🐦‍🔥"
type: "tech"
topics:
  - "dockerdesktop"
  - "mysql"
  - "docker"
  - "dockercompose"
  - "wordpress"
published: false
---

## はじめに

今回は、Docker Composeについて説明します。

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

ちなみに，**Docker Desktopをインストールすると、Docker Composeも自動的にインストールされます。**
私の環境で以下のコマンドを実行し，バージョン確認をしてみます．

```
docker compose version
```

![](/images/sankaku23/1.png)
*最初から入っている*

Docker Desktopって便利ですね〜！

## Docker Composeを使ってみる


![](/images/sankaku23/2.png)

![](/images/sankaku23/3.png)

![](/images/sankaku23/4.png)

![](/images/sankaku23/5.png)

![](/images/sankaku23/6.png)

![](/images/sankaku23/7.png)

![](/images/sankaku23/8.png)

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

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

今回は、Docker Composeについて紹介しました。私もまだまだ慣れていないため，今後も知識を養いながら，色々なことにチャレンジしてみたいです．

**皆さんも素敵なハッピーDockerライフを！！！🌸**
