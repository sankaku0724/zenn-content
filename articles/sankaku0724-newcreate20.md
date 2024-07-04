---
title: "【初学者向け】Dockerネットワークの基礎"
emoji: "🌐"
type: "tech"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "network"
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

私たちはDockerを扱う際に仮想的なネットワークを作ることで、Dockerホストとコンテナ、もしくはコンテナ間で通信することができます。

ここで、[**`docker network`コマンド**](https://docs.docker.jp/engine/userguide/networking/work-with-networks.html)を用いることでDocker上のネットワークの各種設定をすることができます。

まず、Dockerが管理するネットワークの確認をしてみます。Dockerが管理するネットワークは、[**`docker network ls`コマンド**](https://docs.docker.jp/engine/reference/commandline/network_ls.html)で確認できます。

```
docker network ls
```

![](/images/sankaku20/1.png)
*Dockerが管理するネットワーク*

実行画面から分かるように、「bridge」「host」「none」という三つのネットワークが表示されました。[デフォルトでDockerは三つのネットワークを自動的に作成するようになっています](https://docs.docker.jp/engine/userguide/networking/dockernetworks.html#id2)。

それぞれどんな役割を持つのか確認してみます。

## bridgeネットワーク

**[bridgeネットワーク](https://docs.docker.jp/network/bridge.html)はDockerのデフォルトネットワークモード**で、ネットワークオプションを指定しない場合に使用されます。このモードでは、各コンテナのネットワークは独立しており、-pオプションを使用して通信するコンテナを指定します。Dockerホストやコンテナは、1つの仮想的なbridgeネットワークで接続され、IPアドレスが割り当てられます。

### コンテナに割り当てられているIPアドレスの確認

コンテナに対して、どのようなIPアドレスが割り当てられるのかを確認してみます。

#### コンテナの作成

ここでは、2つのhttpdコンテナを起動し、そのコンテナに割り当てられるIPアドレスを確認します。httpdの使い方は以下のサイトに記載されています。

https://hub.docker.com/_/httpd

以下のコマンドでポート番号8080に割り当てた「web01」とポート番号8081に割り当てた「web02」という名前のコンテナをそれぞれ作成します。

```
docker run -dit --name web01 -p 8080:80 httpd:2.4
```

```
docker run -dit --name web02 -p 8081:80 httpd:2.4
```

![](/images/sankaku20/2.png)
*二つのhttpdコンテナを作成*

#### IPアドレスの確認

コンテナが起動したらIPアドレスを確認します。
この際に、Dockerの詳細情報を取得するためのコマンドである[**`docker inspect`コマンド**](https://docs.docker.jp/engine/reference/commandline/inspect.html)を用いてコンテナの情報を抜き出し、さらに--formatオプションを用いてIPアドレスの部分だけを表示させるようにします。

```
docker container inspect --format="{{.NetworkSettings.IPAddress}}" web01
```

```
docker container inspect --format="{{.NetworkSettings.IPAddress}}" web02
```

![](/images/sankaku20/3.png)

これにより、web01には「172.17.0.2」、web02には「172.17.0.3」のIPアドレスが設定されていることが判明しました。

### Dockerコンテナ同士の通信

コンテナはそれぞれ異なるIPアドレスを持ち、bridgeネットワークに接続されています。そのため、コンテナ同士はこのネットワークを通じて互いに自由に通信を行うことができます。

ここで注意が必要です。**Dockerコンテナ間で通信する際は-pオプションを設定する必要がない**ということです。-pオプションはDockerホスト側で受信したデータをそれぞれのDockerコンテナの特定のポートに転送する設定ですが、Dockerホストを経由しない通信の場合には必要ありません。

#### 1. 三つ目のコンテナの作成

実際にコンテナ間で通信できるのか試してみます。
ここでは、三つ目のコンテナを作成し、そのコンテナからweb01とweb02に通信可能かどうかを確認します。

以下のコマンドで新しいubuntuコンテナを作成し、シェルを起動します。実行するとプロンプトが表示され、コンテナ内でコマンド入力ができるようになります。

```
docker run --rm -it ubuntu /bin/bash
```

![](/images/sankaku20/4.png)
*コマンド入力できるように*

#### 2. 必要なものをインストール

コマンド入力できるようになったら、以下のコマンドをそれぞれ入力することでネットワーク関連の操作を可能にします。

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
`iproute2`、`iputils-ping`、および`curl`パッケージをインストールします。

  - `iproute2`: ネットワークインターフェイスやルーティングを管理するためのツールセットであり、`ip`コマンドなどが含まれています。
  - `iputils-ping`: ネットワークの接続性を確認するための `ping`コマンドを提供します。
  - `curl`: URLからデータを取得するためのコマンドラインツールであり、HTTP、HTTPS、FTPなどのプロトコルをサポートしています。

#### 3. IPアドレスを確認する

`ip`コマンドでIPアドレスを確認します。

```
ip address
```

![](/images/sankaku20/5.png)

画像の最後の部分に表示されているeth0インターフェイスのinetの部分を確認すると、IPアドレスは「172.17.0.4」であることが確認できました。

#### 4. pingで疎通確認

既に確認してあるweb01やweb02のIPアドレスに対してpingを送信して疎通確認をします。

```
ping -c 3 172.17.0.2
```

- `-c 3`: 送信するパケットの回数です。この場合、3回のリクエストを送信します。

![](/images/sankaku20/6.png)
*web01へのping送信*

```
ping -c 3 172.17.0.3
```

![](/images/sankaku20/7.png)
*web02へのping送信*

結果が「0% packet loss」となっていることから、通信できていることがわかります。

#### 5. コンテンツを取得

次に、`curl`コマンドを使用してweb01やweb02に接続することで、公開されているWebコンテンツを取得できるかを確認します。

```
curl http://172.17.0.2/
```

```
curl http://172.17.0.3/
```

![](/images/sankaku20/8.png)

「It works!」と表示されていることから、Webコンテンツを取得できていることがわかります。

#### 6. IPアドレス以外で接続確認

ここで、コンテナ名で接続しようとするとどうなるでしょうか。
web01を指定して、`ip`コマンドや`curl`コマンドを使用してみます。

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

### Dockerネットワークを作成してコンテナ名を指定できるようにする

今までの作業で、コンテナはそれぞれ割り当てられたIPアドレスを利用して、互いに通信できることがわかりました。同時に、IPアドレスの代わりとしてコンテナ名を使って通信することはできないということもわかりました。
コンテナに対して、どのようなIPアドレスが割り当てられるのかはコンテナを起動するまでわからないため、コンテナ名で通信相手を指定できないのはとても不便です。また、コンテナを破棄して作り直した場合に、IPアドレスが変わる恐れがあるのも問題です。

ここで、IPアドレスではなくコンテナ名で通信相手を特定できるようにする方法として、**Dockerネットワークを新規に作成する方法**が存在します。
Dockerネットワークを新規に作成することにより、同じネットワーク内のコンテナはコンテナ名で通信できるようになります。

#### 1. Dockerネットワークの新規作成

実際に確認してみます。
Dockerネットワークは[**`docker network create`コマンド**](https://docs.docker.jp/engine/reference/commandline/network_create.html)を用いて作成することができます。以下のコマンドで「mynetwork」という名前のDockerネットワークを新規作成します。

```
docker network create mynetwork
```

![](/images/sankaku20/10.png)
*Dockerネットワークを新規作成*

`docker network ls`コマンドで確認すると、「mynetwork」が作成されていることが確認できました。

![](/images/sankaku20/11.png)
*「mynetwork」が存在している*

ここで、ネットワークの情報を表示する[**`docker network inspect`コマンド**](https://docs.docker.jp/engine/reference/commandline/network_inspect.html)を利用して、「mynetwork」の情報を確認してみます。

```
docker network inspect mynetwork
```

![](/images/sankaku20/12.png)

この情報から、ネットワークのサブネットが「172.18.0.0/16」であり、ゲートウェイのIPアドレスが「172.18.0.1」であることが確認できました。

#### 2. Dockerネットワーク上でコンテナを作成

新規作成したネットワークに参加するコンテナを作ってみます。また、この際に、稼働しているweb01とweb02を破棄し、「mynetwork」に接続するようにして作り直すことにします。

![](/images/sankaku20/13.png)
*web01及びweb02の停止と破棄*

ネットワークに接続させるには、`docker run`の実行時に[--netオプションで接続したいネットワークを指定](https://docs.docker.jp/engine/userguide/networking/work-with-networks.html#connect-containers-network)する必要があります。

```
docker run -dit --name web01 -p 8080:80 --net mynetwork httpd:2.4
```

```
docker run -dit --name web02 -p 8081:80 --net mynetwork httpd:2.4
```

![](/images/sankaku20/14.png)
*接続完了*

`docker network inspect`コマンドでネットワークの情報を確認してみると、web01とweb02が「mynetwork」に接続されていることがわかります。

![](/images/sankaku20/15.png)

#### 3. コンテナ名で接続確認

では、同じネットワーク内のコンテナは本当にコンテナ名で通信できるようになっているのか確認してみます。
今回も三つ目のコンテナを作成し、そのコンテナからweb01とweb02に通信可能かどうかを確認します。この際に--netオプションで「mynetwork」を指定してコンテナを作成します。

```
docker run --rm -it --net mynetwork ubuntu /bin/bash
```

![](/images/sankaku20/16.png)

コマンド入力できるようになったら、以下のコマンドをそれぞれ入力することでネットワーク関連の操作を可能にします。

```
apt update
apt -y upgrade
apt install -y iproute2 iputils-ping curl
```

入力したら、web01やweb02に対してpingを送信してみます。ここでは、先ほどとは違い、IPアドレスではなくコンテナ名を指定してpingを送信してみます。

```
ping -c 3 web01
```

```
ping -c 3 web02
```

![](/images/sankaku20/17.png)
*コンテナ名を指定したping送信*

結果が「0% packet loss」となっていることから、通信できていることがわかります。

また、`curl`コマンドを使用してweb01やweb02に接続することで、公開されているWebコンテンツを取得できるかを確認します。ここでも、IPアドレスではなくコンテナ名を指定します。

```
curl http://web01/
```

```
curl http://web02/
```

![](/images/sankaku20/18.png)

「It works!」と表示されていることから、Webコンテンツを取得できていることがわかります。

### コンテナ名で通信できる仕組み

ここまでの流れで、Dockerネットワークを新規に作成することにより、コンテナ名で通信できるようになることがわかりました。
では、なぜコンテナ名で通信できるようになっているのでしょうか？

それは、**Dockerネットワークで内蔵DNSサーバが提供されるから**です。

[Docker公式ドキュメント日本語訳](https://docs.docker.jp/engine/userguide/networking/dockernetworks.html#docker-dns)を確認すると、以下のように説明されていました。

> Dockerデーモンは内蔵DNSサーバを動かし、ユーザ定義ネットワーク上でコンテナがサービス・ディスカバリを自動的に行えるようにします。コンテナから名前解決のリクエストがあれば、内部DNSサーバを第一に使います。リクエストがあっても内部DNSサーバが名前解決できなければ、外部のDNSサーバにコンテナからのリクエストを転送します。割り当てできるのはコンテナの作成時だけです。内部DNSサーバが到達可能なのは127.0.0.11のみであり、コンテナのresolv.confに書かれます。

`cat`コマンドでコンテナの`resolv.conf`を実際に確認してみると、以下のように表示されました。

![](/images/sankaku20/19.png)
*「`/etc/resolv.conf`」ファイルの中身*

Docker公式ドキュメント日本語訳にも記載されている通り、`nameserver 127.0.0.11`はDockerがコンテナ用に提供する内部DNSサーバーのIPアドレスです。この内部DNSサーバーを使うことで、同じDockerネットワーク内のコンテナ名を名前解決できます。

また、`ExtServers: [192.168.65.7]`から、外部DNSサーバーのIPアドレスが「192.168.65.7」であることもわかりました。

この設定により、Dockerネットワーク内のコンテナはコンテナ名でお互いに通信できるようになります。

### Dockerネットワークの削除

作成した「mynetwork」を削除します。
まず、「mynetwork」を利用しているコンテナを停止した後に破棄します。

![](/images/sankaku20/20.png)
*web01及びweb02の停止と破棄*

コンテナを破棄したら、[**`docker network rm`コマンド**](https://docs.docker.jp/engine/reference/commandline/network_rm.html)を用いて「mynetwork」を削除します。

```
docker network rm mynetwork
```

![](/images/sankaku20/21.png)
*「mynetwork」を削除*

`docker network ls`コマンドで確認すると、「mynetwork」が作成されていることが確認できました。

![](/images/sankaku20/22.png)
*「mynetwork」が存在していない*

ここで、使い終わったイメージも削除しておきます。`docker image ls`でイメージ一覧を表示します。

![](/images/sankaku20/23.png)
*イメージ一覧*

確認したら**本当に消しても良いかどうかをよく確認して**、問題がなければ[イメージを一括削除](https://zenn.dev/joho0724/articles/sankaku0724-newcreate15#%E5%85%A8%E3%81%A6%E3%81%AE%E3%82%A4%E3%83%A1%E3%83%BC%E3%82%B8%E3%82%92%E5%89%8A%E9%99%A4)しましょう。

![](/images/sankaku20/24.png)

## hostネットワーク

**[hostネットワーク](https://docs.docker.jp/network/host.html)は、コンテナがホストのネットワークスタックを直接使用するネットワークモード**です。
**DockerホストのIPアドレスを全Dockerコンテナで共有して使うことができる**ため、ネットワーク性能が重要なアプリケーションやホストのIPアドレスをそのまま使用したい場合に適しています。これにより、ネットワークのオーバーヘッドが少なくなるため、通信速度が向上することがあります。また、コンテナがホストのネットワークを直接使用するため、ネットワークの設定が簡素化されるというメリットもあります。

## noneネットワーク

**[noneネットワーク](https://docs.docker.jp/network/none.html)は、コンテナがネットワークに接続しないネットワークモード**です。
ネットワーク通信を必要としないため、バッチ処理やデータ処理タスクなど、**外部と通信させたくないコンテナを完全に独立した環境で作成できる**というメリットがあります。

## さいごに

ざっくりまとめてみます。

1. **bridgeネットワークはDockerのデフォルトネットワークモード**
ネットワークオプションを指定しない場合に使用されます。Dockerホストやコンテナは、1つの仮想的なbridgeネットワークで接続され、IPアドレスが割り当てられます。また、bridgeネットワークでは、Dockerホスト同士はIPアドレスで接続できます。IPアドレスは、`docker network inspect`や`docker container inspect`で確認できます。

2. **Dockerコンテナ間の通信では-pオプションは必要ない**
Dockerコンテナ間の通信では、-pオプションを指定する必要がありません。コンテナ上の実際のポート番号で通信します。

3. **名前を指定して通信したい場合はDockerネットワークを作る**
コンテナ間の通信をする際に、コンテナ名でアクセスしたい場合には`docker network create`で新しいDockerネットワークを作りましょう。`docker run`を行う際に--netオプションでそのネットワークにコンテナを参加させることにより、コンテナ名でアクセスできるようになります。

4. **hostネットワークとnoneネットワーク**
DockerホストのIPアドレスを全Dockerコンテナで共有して使うhostネットワークと、ネットワークに接続しないnoneネットワークというネットワークがあります。

ここまで記事を読んでくださり、ありがとうございました！今回は、Dockerのネットワークの基礎についてまとめてみました。

今後も、Dockerを理解するために突き進んでいきたいと思います！

**皆さんも素敵なハッピーDockerライフを！！！🌸**
