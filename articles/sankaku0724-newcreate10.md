---
title: "Docker DesktopでWebサーバーを起動する"
emoji: "🐳"
type: "tech"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "container"
  - "web"
published: false
---

## はじめに

:::message
この記事は、Docker初心者である私の解釈に基づいて、Webサーバーの起動方法についてまとめたものです。
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

今回は、私が初めてDockerに触れ、Docker DesktopでWebサーバーを起動した際の手順について紹介します。


### 動作環境

Macbook Air M1上のDocker Desktop

## 　Dockerって何？

**Docker**は大規模なコンテナ環境を管理することを可能にするツールです。
ここでいう**コンテナ**は、アプリケーションやサービスを実行するための軽量な独立した実行環境のことを指します。

## 　1. Docker Hubでイメージを探す

まず、目的のコンテナの元となるイメージを探しました。

Dockerコンテナ内でApache HTTPサーバーを実行するための「**apache**」を検索ボックスに入力し、「**httpd**」を選択して**Pull**しましょう。

**httpd**は、Apache HTTP ServerをDockerコンテナ内で実行するための公式のDockerイメージです。

![](/images/sankaku10/Image1.png)

ページに「**DOCKER OFFICIAL IMAGE**」と書いてあるものは、Docker公式が提供しているイメージであり、信頼性が高いです。

また、詳細ページには実行方法などが記載されているので、よく確認しておきましょう。

![](/images/sankaku10/Image2.png)

## 　2. 実行してみる

Dockerは、以下のような書式でコマンドを扱います。

```
Docker コマンド 操作 オプション
```

先ほどの詳細ページに記載されていた実行コマンドである`docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4`を実行してみます。

```
docker run -dit --name my-apache-app -p 8080:80 -v "$PWD":/usr/local/apache2/htdocs/ httpd:2.4
```

このコマンドを実行すると、**my-apache-app**という名前の新しいコンテナが作成され、そこでApache HTTPサーバーが起動します。また、Apache HTTPサーバーがポート8080で実行され、ホストマシンのカレントディレクトリにあるファイルがApacheのドキュメントルートにマウントされます。

また、実行中のコンテナ一覧は、`docker ps`で確認することができます。

![](/images/sankaku10/run1.png)
*docker psでmy-apache-appという名前のコンテナが動いているのを確認*

また、余談ですが、docker psコマンドに-aオプションを指定すると、稼働中ではないものも含むすべてのコンテナ一覧を確認することもできます。

```
docker ps -a
```

コンテナが実行状態の時に、[http://localhost:8080](http://localhost:8080/)にアクセスすると、**docker runを実行した時のカレントディレクトリの内容が表示されます。**

![](/images/sankaku10/run2.png)
*私のディレクトリの内容*

## 　3. Webサーバーを起動だ！

**docker runを実行した時のカレントディレクトリの内容が表示される⋯**

**ということは⋯！**

そうです！例えば、**ディレクトリに「index.html」ファイルを置くことでそれを表示させることができます！**

では、ディレクトリ上にindex.htmlを作成してみましょう。

以下のコマンドを使用すると、nanoエディタでhtmlファイルを編集することができます。

```
nano index.html
```

もちろん、他のエディタで作成しても問題ありません。

index.htmlに以下のように書き込んでみます。

![](/images/sankaku10/indexhtml.png)

nanoエディタの場合は、編集後にCtrl+xで保存するかどうかを決め（yがYes、nがNo）、その後、ファイル名を求められた時に何も変更せずにEnterキーを押すと「index.html」を保存することができます。

そして、もう一度[http://localhost:8080](http://localhost:8080/)にアクセスすると、コンテンツが更新され、index.htmlの内容が表示されます。

![](/images/sankaku10/indexhtml2.png)

これによって、Webサーバーの起動ができました！

## 　4. コンテナの停止と再稼働

先ほど動かしたコンテナは、`docker stop`コマンドで停止させることができます。

docker stop (コンテナ名orコンテナID)を実行することで停止させてみましょう。

```
docker stop my-apache-app
```

`docker ps`で確認すると、稼働中のコンテナが存在していないことが確認できます。

![](/images/sankaku10/stop.png)

ここで、コンテナは停止しますが、`docker ps -a`コマンドで確認するとコンテナを確認することができるため、完全になくなってしまったわけではないことがわかります。

そのため、`docker stop`で停止したコンテナを再稼働させることができます。

再稼働させたい際は、`docker start`コマンドを用いましょう。
`docker start (コンテナ名orコンテナID)`を実行することで、コンテナを再稼働させることができます。

```
docker start my-apache-app
```

`docker ps`で確認すると、コンテナが稼働中であることが確認できます。

![](/images/sankaku10/restart.png)

もちろん、稼働しているため、先ほどと同様にWebブラウザで[http://localhost:8080](http://localhost:8080/)にアクセスすることができます。

## 　5. ログの確認

実行中のログを確認したい場合には、`docker logs`コマンドを用いましょう。
`docker logs (コンテナ名orコンテナID)`を実行することで、ログを確認することができます。

```
docker logs my-apache-app
```

## 　6. コンテナの破棄

`stop`や`start`でコンテナの停止と再稼働をさせることができますが、再稼働できるということは`docker stop`を実行しても、コンテナが残り続けているということです。
これは明らかにディスクを圧迫してしまいます。

コンテナをもう使わないのであれば、停止させるだけではなく**破棄**しましょう。

コンテナの破棄は、コンテナが停止状態の時に行う必要があるため、**必ず`docker stop`を実行した後に**`docker rm`コマンドを用いましょう。

```
docker rm my-apache-app
```

こうすることで、完全にDockerコンテナを破棄することに成功しました。

## 　7. Dockerイメージの破棄

コンテナを破棄したことで全て終わらせた気になってはいけません。
Dockerイメージもディスクを圧迫してしまう要因になります。

イメージをもう使わないのであれば、コンテナと同じく**破棄**しちゃいましょう。

ダウンロードしたDockerイメージの確認をしたい場合は、`docker image ls`コマンドを用いましょう。

```
docker image ls
```

これによって、**httpd:2.4**イメージ（httpdというイメージのバージョン2.4）がDockerに存在することが確認できました。

![](/images/sankaku10/imagerm.png)

一般的にDockerイメージはコンテナを破棄したとしても、新しくコンテナを作る際に再ダウンロードする必要がないようにするために残ったままになってしまいます。
今回の私の場合では、Dockerイメージが188MBの容量を持っていることが確認できました。

Dockerイメージの削除をしたい場合は、`docker image rm`コマンドを用いましょう。

```
docker image rm httpd:2.4
```

これによって、httpd:2.4イメージを削除することができます。

これでコンテナの構築作業の一連の流れは終了です！

## さいごに
ここまで記事を読んでくださり、ありがとうございました！
今回、初めてDockerに触れてみましたが、これからもっと色々なことにチャレンジしていきたいです！

**皆さんも素敵なハッピーDockerライフを！！！🌸**
