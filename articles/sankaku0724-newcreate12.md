---
title: "【初学者向け】Dockerコンテナの基本操作"
emoji: "🐋"
type: "tech"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "container"
  - "初心者"
published: false
---

## はじめに

この記事は、この記事は，以前私が書いた「[【初学者向け】DockerでWebサーバーを起動する](https://zenn.dev/joho0724/articles/sankaku0724-newcreate10)」という記事の内容を踏まえて、Dockerの基本操作についてまとめたものです。

:::message
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- Docker Desktop 4.21.1 (114176)
- Docker Engine 24.0.2
- Docker Compose v2.19.1
- MacOS Sonoma 14.4.1

## Dockerコマンド一覧

Dockerのコマンド一覧は，以下の記事に記載されています。この記事は，Docker公式ドキュメントを有志の方々が日本語に翻訳してくれているものです．

https://docs.docker.jp/engine/reference/commandline/index.html

公式による最新のドキュメントを確認したい人は，以下のリンクから飛ぶことができます．

https://docs.docker.com/

## 1.コンテナの起動から破棄までの流れ

まず，コンテナが起動するまでの流れを追っていきたいと思います．
私は以前，[Docker上でWebサーバーを起動した際](https://zenn.dev/joho0724/articles/sankaku0724-newcreate10)に以下のようにrunコマンドを使用しました．

```
docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

この時に使用した`docker run`コマンドは，`docker pull`，`docker create`，`docker start`の三つのコマンドを複合した機能を持つコマンドであり，上のコマンドと下の三つのコマンドは同じ意味を持ちます．

```
docker pull httpd:2.4
docker create --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
docker start my-apache-app
```

この三つのコマンドを一つづつ見ていきましょう．

### docker pull

`docker pull`コマンドはイメージを取得する際に用いるものであり，

```
docker pull イメージ名orイメージID
```

といった書式で使用できます．

また，先ほどの`docker pull httpd:2.4`はhttpdの「2.4」というバージョンのイメージをダウンロードするということを表しています．

#### 最新版を意味する「latest」

ここで`docker pull httpd:2.4`を`docker pull httpd`のように[タグ名を省略した場合](https://docs.docker.jp/engine/reference/commandline/pull.html#docker-pull-an-image-from-docker-hub)，最新版を意味する「latest」というタグがつけられたとみなされ，最新のバージョンのイメージがインストールされます．

どうしても特定のバージョンを使いたい時以外は，タグを省略しても良さそうです．

### docker create

`docker create`コマンドはイメージからコンテナを作成する際に用いるものであり，

```
docker create オプション イメージ名orイメージID 実行したいコマンド
```

といった書式で使用できます．

ここで，`docker create --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4`は実行したいコマンドを省略している代わりにhttpdコンテナの制作者が定めたWebサーバーの通信をするためのコマンドが自動的に実行されます．

また，上記のコマンドでは，「**--name**」，「**-p**」，「**-v**」の三つのオプションが使用されています．

#### 「--name」オプション

「--name」オプションはコンテナ名をつける際に使用します．
先ほどのコマンドでは，`--name my-apache-app`とすることで作成したコンテナを「my-apache-app」というコンテナ名で利用できるようにしています．

#### 「-p」オプション

「-p」オプションはポート番号をマッピングする際に用いるものであり，

```
-p ホストのポート番号:コンテナのポート番号
```

といった書式で使用できます．

先ほどのコマンドでは「-p 8080:80」と指定しており，DockerホストのTCPポート8080番をコンテナの80番に結びつけています．

httpdイメージの制作者は、イメージをポート80番で待ち受け、そこからApacheに渡すように構成しています。そのため、このようにポートのマッピングを設定することで、「[http://DockerホストのIP:8080/](http://localhost:8080/)」でアクセスすると、その通信がコンテナのポート80番に転送され、Apacheが公開している内容が見えるようになります。実際に私は以前このURLにアクセスして[Webサーバーの起動を確認しました](https://zenn.dev/joho0724/articles/sankaku0724-newcreate10#3.-web%E3%82%B5%E3%83%BC%E3%83%90%E3%83%BC%E3%82%92%E8%B5%B7%E5%8B%95%E3%81%A0%EF%BC%81)。

Dockerでは「-p」オプションを指定しない限り、DockerホストとDockerコンテナとの通信はつながりません。Dockerホストを通じてDockerコンテナ内で動いているプログラムと通信するには、「-p」オプションの設定が必要になります．

ちなみにマッピングの状態は、`docker port`コマンドで確認できます。

#### 「-v」オプション

「-v」オプションは、コンテナの特定のディレクトリにホストのディレクトリをマウントする際に用いるものであり，

```
-v ホストのディレクトリ:コンテナのディレクトリ
```

といった書式で使用できます．

httpdコンテナの起動では、`-v "$PWD":/usr/local/apache2/htdocs/`といったように「-v」オプションを指定しており，$PWDの値をコンテナの/usr/local/apache2/htdocs/に割り当てています．

ここで，`$PWD`はdockerコマンドを入力したホスト側のカレントディレクトリを示す環境変数であり，`/usr/local/apache2/htdocs`はコンテナ側のマウント先のディレクトリです。ここでのマウントとは、あるディレクトリに対して別のディレクトリを被せて、そのディレクトリの内容が見えるようにする設定のことです。

### docker start

`docker create`で作成したコンテナはまだ何も動作していません．

そこで`docker start`コマンドを使用することでコンテナを動かすことができます．
`docker start`コマンドは，

```
docker start コンテナ名orコンテナID
```

といった書式で使用できます．

ここまで，「**--name**」，「**-p**」，「**-v**」の三つのオプションについて説明しましたが，単一で動かすことはほとんどないと思われます．複合した機能を持つ`docker run`コマンドについての理解を深めておくと良さそうですね．

`docker start`コマンドで開始されたコンテナは`docker stop`コマンドを使用することで停止することができます．
`docker stop`コマンドは，

```
docker stop コンテナ名orコンテナID
```

といった書式で使用できます．

これで，コンテナの起動から破棄までの流れの確認は以上です．

## 2.コンテナのデタッチとアタッチ

稼働中のコンテナは何かしらのコマンドを実行しっぱなしの状態になっています．つまり，**普通であれば`docker run`した後には，他のコマンドを打てなくなる**はずです．

しかし，`docker run`を実行した後に，他のコマンド（`docker stop`など）を打ち込んで実行することが可能なのはなぜでしょう．

それは**コンテナがバックグラウンドで動いているから**です．

コンテナをバックグラウンドで動かすための指定として「-dit」オプションというものがあります．

では，「-dit」オプションをつけずに実行するとどうなるのか試すために，先ほどのrunコマンドから「-dit」オプションを外して実行します，

```
docker run --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```



![](/images/sankaku10/run1.png)




## 3.コンテナのメンテナンス
