---
title: "【初学者向け】Dockerネットワークの基礎と実践"
emoji: "🌐"
type: "tech"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "container"
  - "ネットワーク"
published: false
---

## はじめに

今回は、Dockerのネットワークについて説明します。

:::message
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- Docker Desktop 4.30.0 (149282)
- Docker Engine 26.1.1
- Docker Compose v2.27.0-desktop.2
- MacOS Sonoma 14.5

## Dockerコマンド一覧

Dockerのコマンド一覧は、以下のサイトに記載されています。このサイトは、Docker公式ドキュメントを有志の方々が日本語に翻訳してくれているものです。

https://docs.docker.jp/engine/reference/commandline/index.html

公式による最新のドキュメントを確認したい人は、以下のリンクから飛ぶことができます。

https://docs.docker.com/

## Dockerネットワーク

私たちはDockerを扱う際に仮想的なネットワークを作ることで，Dockerホストとコンテナ、もしくはコンテナ間で通信することができます。

ここで，[**`docker network`コマンド**](https://docs.docker.jp/engine/userguide/networking/work-with-networks.html)を用いることでDocker上のネットワークの各種設定をすることができます．

まず，Dockerが管理するネットワークの確認をしてみます．Dockerが管理するネットワークは、`docker network ls`コマンドで確認できます。

```
docker network ls
```

![](/images/sankaku20/1.png)
*Dockerが管理するネットワーク*

実行画面から分かるように、「bridge」「host」「none」という三つのネットワークが表示されました．[デフォルトでDockerは三つのネットワークを自動的に作成するようになっています](https://docs.docker.jp/engine/userguide/networking/dockernetworks.html#id2)。

それぞれどんな役割を持つのか確認してみましょう．

## bridgeネットワーク

**[bridgeネットワーク](https://docs.docker.jp/network/bridge.html)はDockerのデフォルトネットワークモード**で、ネットワークオプションを指定しない場合に使用されます。このモードでは、各コンテナのネットワークは独立しており、-pオプションを使用して通信するコンテナを指定します。Dockerホストやコンテナは、1つの仮想的なbridgeネットワークで接続され、IPアドレスが割り当てられます。

### コンテナに割り当てられているIPアドレスの確認

コンテナに対して、どのようなIPアドレスが割り当てられるのかを確認してみます。

#### コンテナの作成

ここでは、2つのhttpdコンテナを起動し、そのコンテナに割り当てられるIPアドレスを確認します。httpdの使い方などは以下のサイトを確認してください。

https://hub.docker.com/_/httpd

以下のコマンドでポート番号8080に割り当てた「web01」とポート番号8081に割り当てた「web02」という名前のコンテナをそれぞれ作成します．

```
docker run -dit --name web01 -p 8080:80 httpd:2.4
```

```
docker run -dit --name web02 -p 8081:80 httpd:2.4
```

![](/images/sankaku20/2.png)
*二つのhttpdコンテナを作成*

#### IPアドレスの確認

コンテナが起動したらIPアドレスを確認します．
この際に，Dockerコンテナの詳細情報を取得するためのコマンドである[**`docker container inspect`コマンド**](https://docs.docker.jp/engine/reference/commandline/inspect.html)を用い，--formatオプションをつけることでIPアドレスの部分だけを表示させるようにします．

```
docker container inspect --format="{{.NetworkSettings.IPAddress}}" web01
```

```
docker container inspect --format="{{.NetworkSettings.IPAddress}}" web02
```

![](/images/sankaku20/3.png)

これにより，web01は「172.17.0.2」，web02は「172.17.0.3」というIPアドレスが設定されていることが判明しました．

### Dockerコンテナ同士の通信

コンテナはそれぞれ異なるIPアドレスを持ち、bridgeネットワークに接続されています。そのため，コンテナ同士はこのネットワークを通じて互いに自由に通信を行うことができます．

ここで注意する必要があります．**Dockerコンテナ間で通信する際は，-pオプションを設定する必要がない**ということです．-pオプションはDockerホスト側で受信したデータをそれぞれのDockerコンテナの特定のポートに転送する設定ですが、Dockerホストを経由しない通信の場合には必要ないからです．

#### 1. 三つ目のコンテナの作成

実際にコンテナ間で通信できるのか試してみます。
ここでは、三つ目のコンテナを作成し、そのコンテナからweb01とweb02に通信可能かどうかを確認します．

以下のコマンドで新しいubuntuコンテナを作成し，シェルを起動します。実行するとプロンプトが表示され、コンテナ内でコマンド入力ができるようになります。

```
docker run --rm -it ubuntu /bin/bash
```

![](/images/sankaku20/4.png)
*コマンド入力できるように*

#### 2. 必要なものをインストール

コマンド入力できるようになったら，以下のコマンドをそれぞれ入力することでネットワーク関連の操作を可能にします．

```
apt update
apt -y upgrade
apt install -y iproute2 iputils-ping curl
```
- **`apt update`**
パッケージリストを最新の状態に更新します。これにより、システムは最新の利用可能なパッケージ情報を取得します。

- **`apt -y upgrade`**
インストールされているすべてのパッケージを最新バージョンにアップグレードします。-yオプションは、すべての質問に対して自動的に「yes」と答えるため、ユーザーの確認を省略します。

- **`apt install -y iproute2 iputils-ping curl`**
`iproute2`、`iputils-ping`、および`curl`パッケージをインストールします。-yオプションは、インストールに際しての確認を自動的に「yes」としてスキップします。

  - `iproute2`: ネットワークインターフェイスやルーティングを管理するためのツールセットであり，`ip`コマンドなどが含まれています。
  - `iputils-ping`: ネットワークの接続性を確認するための `ping`コマンドを提供します。
  - `curl`: URLからデータを取得するためのコマンドラインツールであり，HTTP、HTTPS、FTPなどのプロトコルをサポートしています。

#### 3. IPアドレスを確認する

`ip`コマンドでIPアドレスを確認します．

```
ip address
```

![](/images/sankaku20/5.png)

画像の最後の部分に表示されているeth0インターフェイスのinetの部分を確認すると，IPアドレスは「172.17.0.4」であることが確認できました。

#### 4. pingで疎通確認

既に確認してあるweb01コンテナやweb02コンテナのIPアドレスに対してpingを送信して疎通確認をします。

```
ping -c 3 172.17.0.2
```

- `-c 3`: 送信するパケットの回数を指定します。この場合、3回のリクエストを送信します。

![](/images/sankaku20/6.png)
*web01へのping送信*

```
ping -c 3 172.17.0.3
```

![](/images/sankaku20/7.png)
*web02へのping送信*


結果が「0% packet loss」となっていることから，疎通できていることがわかります．

#### 5. コンテンツを取得

次に，`curl`コマンドを使用してweb01やweb02に接続することで、公開されているWebコンテンツを取得できるかを確認します．

```
curl http://172.17.0.2/
```

```
curl http://172.17.0.3/
```

![](/images/sankaku20/8.png)

「It works!」と表示されていることから，Webコンテンツを取得できていることがわかります．

#### 6. IPアドレス以外で接続確認

ここで，コンテナ名で接続しようとするとどうなるでしょうか．
web01を指定して，`ip`コマンドや`curl`コマンドを使用してみます．

```
ping -c 3 web01
```

```
curl http://web01/
```

![](/images/sankaku20/9.png)
*実行画面*

どちらも同様に、**web01が見つからないというエラーが発生**します。

#### 7. 作業終了

`exit`と入力して終了します。

```
exit
```

### Dockerネットワークの新規作成

このように、それぞれのコンテナは、割り当てられたIPアドレスを介して、互いに通信できることがわかりました。同時に、IPアドレスの代わりにコンテナ名を使って通信することはできないこともわかりました。
コンテナ名で通信相手を指定できないのは、とても不便です。なぜなら、コンテナに対して、どのようなIPアドレスが割り当てられるのかはコンテナを起動するまでわかりませんし、コンテナを破棄して作り直せば、IPアドレスが変わる恐れがあるためです。実験や開発目的ならともかく、実運用まで考えたときは、IPアドレスではなくてコンテナ名で通信相手を特定できるほうが望ましいといえます。
そのための方法は、2つあります。1つは、Dockerネットワークを新規に作成する方法、もう1つは
-linkオプションを指定する方法です。後者の方法は非推奨なのでコラムで紹介することにし、以下では、前者の方法を説明していきます。


```
docker run -dit --name web01 -p 8080:80 --net mynetwork httpd:2.4
```



![](/images/sankaku20/10.png)

![](/images/sankaku20/11.png)

![](/images/sankaku20/12.png)

![](/images/sankaku20/13.png)

![](/images/sankaku20/14.png)

![](/images/sankaku20/15.png)

![](/images/sankaku20/16.png)

![](/images/sankaku20/17.png)

![](/images/sankaku20/18.png)

![](/images/sankaku20/19.png)

![](/images/sankaku20/20.png)

![](/images/sankaku20/21.png)

![](/images/sankaku20/22.png)

![](/images/sankaku20/23.png)

![](/images/sankaku20/24.png)


## hostネットワーク

**[hostネットワーク](https://docs.docker.jp/network/host.html)は，コンテナがホストのネットワークスタックを直接使用するネットワークモード**です。
**DockerホストのIPアドレスを全Dockerコンテナで共有して使うことができる**ため，ネットワーク性能が重要なアプリケーションやホストのIPアドレスをそのまま使用したい場合に適しています．これにより，ネットワークのオーバーヘッドが少なくなるため、通信速度が向上することがあります．また，コンテナがホストのネットワークを直接使用するため、ネットワークの設定が簡素化されるというメリットもあります．

## noneネットワーク

**[noneネットワーク](https://docs.docker.jp/network/none.html)は，コンテナがネットワークに接続しないネットワークモード**です。
ネットワーク通信を必要としないため，バッチ処理やデータ処理タスクなど、**外部と通信させたくないコンテナを完全に独立した環境で作成できる**というメリットがあります．

## さいごに

ざっくりまとめてみます．

1. **bridgeネットワークはDockerのデフォルトネットワークモード**
ネットワークオプションを指定しない場合に使用されます。Dockerホストやコンテナは、1つの仮想的なbridgeネットワークで接続され、IPアドレスが割り当てられます。また，bridgeネットワークでは、Dockerホスト同士はIPアドレスで接続できます。IPアドレスは、`docker network inspect`や`docker container inspect`で確認できます。

2. **コンテナ間の通信では-pオプションは必要ない**
コンテナ間の通信では、-pオプションを指定する必要がありません．コンテナ上の実際のポート番号で通信します。

3. **名前を指定して通信したい場合はDockerネットワークを作る**
コンテナ間の通信をする際に、コンテナ名でアクセスしたい場合には`docker network create`で新しいDockerネットワークを作りましょう．`docker run`する際に、--netオプションでそのネットワークにコンテナを参加させることにより、コンテナ名でアクセスできるようになります。

4. **hostネットワークとnoneネットワーク**
DockerホストのIPアドレスを全Dockerコンテナで共有して使うhostネットワークと、ネットワークに接続しないnoneネットワークというネットワークがあります。

ここまで記事を読んでくださり、ありがとうございました！今回は、Dockerのネットワークの基礎についてまとめてみました。

今後も、Dockerを理解するために突き進んでいきたいと思います！

**皆さんも素敵なハッピーDockerライフを！！！🌸**
