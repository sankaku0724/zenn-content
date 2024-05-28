---
title: "【初学者向け】Dockerバインドマウントの基礎と実践"
emoji: "⛓️"
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

この記事では、Dockerのバインドマウントについて説明します。

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

## バインドマウントって何？

[**Dockerのバインドマウント**](https://docs.docker.jp/storage/bind-mounts.html)は、コンテナとホストシステムのファイルシステムを共有するための機能です。これにより、ホスト上の特定のディレクトリやファイルをコンテナ内にマウントすることができます。

バインドマウントを使用すると、ホストマシン上のディレクトリをコンテナ内のディレクトリにリンクさせることができます。このリンクにより、ホスト側で変更されたファイルはリアルタイムでコンテナ側にも反映され、逆もまた然りです。

## Dockerコンテナとファイルの独立性

Dockerのバインドマウントを行う前に，Dockerコンテナとファイルの独立性について理解しておくことが大切です．
Dockerコンテナは、それぞれが隔離された実行環境です。コンテナを破棄した場合、その中にあるファイルは失われます。いくつ起動しても、それらが互いに影響を受けることはありません。

### 2つのコンテナを作成してみる

ここでhttpdイメージを利用し，2つのコンテナを作成してみます。httpdの使い方などは以下のサイトを確認してください。

https://hub.docker.com/_/httpd

コンテナの名前はそれぞれweb01とweb02とします。web01はポート8080、web02はポート8081にマッピングすることにします。また、どちらもオプションを指定せず、マウントを行わないようにします。そうすると、この2つのコンテナは完全に互いに独立します。

```
docker run -dit --name web01 -p 8080:80 httpd:2.4
```

![](/images/sankaku17/1.png)  
*一つ目のコンテナ作成*

```
docker run -dit --name web02 -p 8081:80 httpd:2.4
```

![](/images/sankaku17/2.png)
*二つ目のコンテナ作成*

`docker ps`コマンドで確認すると，どちらのコンテナも実行中であることが確認できました．

![](/images/sankaku17/3.png)

:::message
`docker ps`コマンドで「-a」オプションを指定すると、稼働中ではないものも含むすべてのコンテナ一覧を確認することができます。
:::

これでApacheが実際に2つ起動しているはずです。ブラウザから「[http://Dockerホスト:8080/](http://localhost:8080/)」および「[http://Dockerホスト:8081/](http://localhost:8081/)」に接続して確認すると，どちらにも接続でき「It works!」と表示されました。

![](/images/sankaku17/4.png)
*[http://Dockerホスト:8080/](http://localhost:8080/)に接続した場合*

このようにして、1台のDockerホストに2台のWebサーバーを同居させることができました。もちろん必要があれば、さらに3台、4台といったようにWebサーバーを追加できます。1台のマシンに複数のWebサーバーを同居させることができるのはとても素晴らしいことですよね．

### コンテナ内にファイルをコピーする

先ほど2つのコンテナを作成しましたが，どちらもまだコンテンツファイルを置いていないので両方とも「It works！」と表示され区別が付きません。そこでindex.htmlファイルを置いて、片方を「It's web01!」、もう片方を「It's web02!」と表示できるようにしてみます。

ここで，コンテナとローカルファイルシステム間でファイルやフォルダをコピーできる[**`docker cp`コマンド**](https://docs.docker.jp/engine/reference/commandline/cp.html)というものが存在します．これによって，index.htmlをDockerホストに作成し，それをコンテナにコピーすることで表示を変更できるようにしてみます．

#### 1./tmpディレクトリにindex.htmlを作成する

まずはindex.htmlファイルを/tmpディレクトリ内に作成します。
次のようにして/tmpディレクトリにカレントディレクトリを移動します。ここで、後で現在のカレントディレクトリに戻れるように、[**`push`コマンド**](https://linuxize.com/post/popd-and-pushd-commands-in-linux/)を使ってディレクトリを移動することにします。

:::message
`push`コマンドは、現在のカレントディレクトリの状態を保存した上で、別の場所にカレントディレクトリを移動するコマンドです。`popd`と入力すると，保存したカレントディレクトリの位置まで戻ることができます（後述の手順［8］）。
:::

```
pushd /tmp
```

![](/images/sankaku17/5.png)

これによって，/tmpディレクトリに移動できました．

ここから，web01コンテナ用のindex.htmlファイルを用意します．nanoエディタなどでindex.htmlを作成します．

```
nano index.html
```

index.htmlの中身は以下のようにします．

```html:index.html
<html>
<body>
<div>It's web01!</div>
</body>
<html>
```

これによって，「It's web01!」と表示するようにします．

#### 2.ファイルをコンテナにコピーする

このindex.htmlをコンテナweb01の/usr/local/apache2/htdocs/にコピーするために，次のように実行してみます．

```
docker cp /tmp/index.html web01:/usr/local/apache2/htdocs/
```

![](/images/sankaku17/6.png)
*「Sucessfully」と出ていることから，コピーが成功していることが確認できる*

#### 3.ブラウザで確認してみる

ブラウザで[http://Dockerホスト:8080/](http://localhost:8080/)に接続してみます．

![](/images/sankaku17/7.png)

すると，コピーしたindex.htmlの内容である「It's web01!」の表示が確認できました．

#### 4.コンテナの内部に入って確認してみる

本当に`docker cp`コマンドでindex.htmlの内容がコピーされているのか，コンテナの内部に入って確認してみます．
実行中のコンテナの中を操作するには[`docker exec`コマンドを使えば良い](https://zenn.dev/joho0724/articles/sankaku0724-newcreate12#%E5%AE%9F%E8%A1%8C%E4%B8%AD%E3%81%AE%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%A7%E3%82%B7%E3%82%A7%E3%83%AB%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B)ので，それを用いてweb01コンテナの内部に入ってみます．

```
docker exec -it web01 /bin/bash
```
コンテナの内部に入って`cat`コマンドでindex.htmlの内容を確認すると，`docker cp`コマンドでコピーした内容と一致していることが確認できました．

![](/images/sankaku17/8.png)  

確認が終わったので，`exit`でコンテナの外に出ます．

![](/images/sankaku17/9.png)  

#### 5.web02も同様に行う

コンテナweb02に関しても同様に確認してみます．まず，/tmpディレクトリにindex02.htmlとして以下の内容を保存します．

```html:index02.html
<html>
<body>
<div>It's web02!</div>
</body>
<html>
```

このindex02.htmlという名前のファイルをコンテナにindex.htmlとしてコピーします．

```
docker cp /tmp/index02.html web02:/usr/local/apache2/htdocs/index.html
```

![](/images/sankaku17/10.png)
*「Sucessfully」と出ていることから，コピーが成功していることが確認できる*  


ブラウザで[http://Dockerホスト:8081/](http://localhost:8081/)に接続してみます．

![](/images/sankaku17/11.png) 

すると，コピーしたindex02.htmlの内容である「It's web02!」の表示が確認できました．

#### 6.カレントディレクトリを戻す

```
popd
```

![](/images/sankaku17/12.png)  

![](/images/sankaku17/13.png)  

![](/images/sankaku17/14.png)  

![](/images/sankaku17/15.png)  

![](/images/sankaku17/16.png)  

![](/images/sankaku17/17.png)  

![](/images/sankaku17/18.png)  

![](/images/sankaku17/19.png)  

![](/images/sankaku17/20.png)  

![](/images/sankaku17/21.png)  

![](/images/sankaku17/22.png)  

![](/images/sankaku17/23.png)  

![](/images/sankaku17/24.png)  

![](/images/sankaku17/25.png)  

![](/images/sankaku17/26.png)  

![](/images/sankaku17/27.png)


## さいごに


5-2まで

バインドマウントの特徴
リアルタイム同期: ホストとコンテナ間でファイルやディレクトリがリアルタイムで同期されます。
パフォーマンス: バインドマウントは、ホストとコンテナが同じファイルシステムを使用するため、パフォーマンスが高いです。
柔軟性: ホスト上の任意のディレクトリをコンテナにマウントできるため、柔軟に使用できます。

注意点
セキュリティ: ホストシステムのファイルシステムに対する読み書きアクセスをコンテナに許可するため、セキュリティリスクが増加する可能性があります。
依存性: バインドマウントを使用する場合、コンテナはホストのディレクトリ構造に依存するため、移植性が低下する可能性があります。
使用例
開発環境での使用
開発中のアプリケーションコードをコンテナ内で実行する場合、バインドマウントを使用することで、コードの変更が即座に反映されるため便利です。


データ永続化
データベースなどのサービスのデータを永続化するために、バインドマウントを使用することができます。


まとめ
バインドマウントは、ホストとコンテナ間でディレクトリやファイルを共有するための強力な機能です。リアルタイムの同期や高いパフォーマンスを提供する一方で、セキュリティや移植性に関する注意が必要です。適切に使用することで、開発効率の向上やデータの永続化に役立ちます。


ここまで記事を読んでくださり、ありがとうございました！今回は、ZennのPV数をGoogle Analyticsで確認する方法について紹介しました。興味を持った方は、ぜひGoogle Analyticsを利用してZennの執筆のモチベーションを上げていきましょう！

**皆さんも素敵なハッピーDockerライフを！！！🌸**

