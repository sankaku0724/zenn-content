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
published: true
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

バインドマウントを使用すると、ホストマシン上のディレクトリをコンテナ内のディレクトリにリンクさせることができます。

## Dockerコンテナとファイルの独立性

バインドマウントを行う前に、Dockerコンテナとファイルの独立性について理解しておくことが大切です。
Dockerコンテナは、それぞれが隔離された実行環境です。**コンテナを破棄した場合、その中にあるファイルは失われます**。いくつ起動しても、それらが互いに影響を受けることはありません。

### 2つのコンテナを作成してみる

ここでhttpdイメージを利用し、2つのコンテナを作成してみます。httpdの使い方などは以下のサイトを確認してください。

https://hub.docker.com/_/httpd

コンテナの名前はそれぞれweb01とweb02とします。web01はポート8080、web02はポート8081にマッピングすることにします。また、どちらもオプションを指定せず、マウントを行わないようにすると、この2つのコンテナは完全に互いに独立します。

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

`docker ps`コマンドで確認すると、どちらのコンテナも実行中であることが確認できました。

![](/images/sankaku17/3.png)

:::message
`docker ps`コマンドで「-a」オプションを指定すると、稼働中ではないものも含むすべてのコンテナ一覧を確認することができます。
:::

これでApacheが実際に2つ起動しているはずです。ブラウザから「[http://Dockerホスト:8080/](http://localhost:8080/)」および「[http://Dockerホスト:8081/](http://localhost:8081/)」に接続して確認すると、どちらにも接続でき「It works!」と表示されました。

![](/images/sankaku17/4.png)
*[http://Dockerホスト:8080/](http://localhost:8080/)に接続した場合*

このようにして、1台のDockerホストに2台のWebサーバーを同居させることができました。

### コンテナ内にファイルをコピーする

先ほど2つのコンテナを作成しましたが、どちらもまだコンテンツファイルを置いていないので両方とも「It works!」と表示され区別が付きません。そこでindex.htmlファイルを置いて、片方を「It's web01!」、もう片方を「It's web02!」と表示できるようにしてみます。

ここで、コンテナとローカルファイルシステム間でファイルやフォルダをコピーできる[**`docker cp`コマンド**](https://docs.docker.jp/engine/reference/commandline/cp.html)というものが存在します。これを用いて、index.htmlをDockerホストに作成し、それをコンテナにコピーすることで表示を変更できるようにしてみます。

#### 1.`/tmp`ディレクトリにindex.htmlを作成する

まずはindex.htmlファイルを`/tmp`ディレクトリ内に作成します。
ここで`/tmp`ディレクトリにカレントディレクトリを移動する際に、後で現在のカレントディレクトリに戻れるように、[**`pushd`コマンド**](https://linuxize.com/post/popd-and-pushd-commands-in-linux/)を使ってディレクトリを移動することにします。

:::message
`pushd`コマンドは、現在のカレントディレクトリの状態を保存した上で、別の場所にカレントディレクトリを移動するコマンドです。`popd`と入力すると、保存したカレントディレクトリの位置まで戻ることができます。
:::

```
pushd /tmp
```

![](/images/sankaku17/5.png)

これによって、`/tmp`ディレクトリに移動できました。

ここから、web01コンテナ用のindex.htmlファイルをnanoエディタなどで作成して用意します。

```
nano index.html
```

また、index.htmlの中身は以下のようにし、「It's web01!」と表示するようにします。

```html:index.html
<html>
<body>
<div>It's web01!</div>
</body>
<html>
```

#### 2.ファイルをコンテナにコピーする

このindex.htmlをコンテナweb01の`/usr/local/apache2/htdocs/`にコピーするために、次のように実行してみます。

```
docker cp /tmp/index.html web01:/usr/local/apache2/htdocs/
```

![](/images/sankaku17/6.png)
*「Sucessfully」と出ていることから、コピーが成功していることが確認できる*

#### 3.ブラウザで確認してみる

ブラウザで[http://Dockerホスト:8080/](http://localhost:8080/)に接続すると、コピーしたindex.htmlの内容である「It's web01!」の表示が確認できました。

![](/images/sankaku17/7.png)

#### 4.コンテナの内部に入って確認してみる

本当に`docker cp`コマンドでindex.htmlの内容がコピーされているのか、コンテナの内部に入って確認してみます。
実行中のコンテナの中を操作するには[`docker exec`コマンドを使えば良い](https://zenn.dev/joho0724/articles/sankaku0724-newcreate12#%E5%AE%9F%E8%A1%8C%E4%B8%AD%E3%81%AE%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%A7%E3%82%B7%E3%82%A7%E3%83%AB%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B)ので、それを用いてweb01コンテナの内部に入ってみます。

```
docker exec -it web01 /bin/bash
```
コンテナの内部に入って`cat`コマンドでindex.htmlの内容を確認すると、`docker cp`コマンドでコピーした内容と一致していることが確認できました。

![](/images/sankaku17/8.png)  

確認が終わったので、`exit`でコンテナの外に出ます。

![](/images/sankaku17/9.png)  

#### 5.web02も同様に行う

コンテナweb02に関しても同様に確認してみます。まず、`/tmp`ディレクトリにindex02.htmlとして以下の内容を保存します。

```html:index02.html
<html>
<body>
<div>It's web02!</div>
</body>
<html>
```

このindex02.htmlという名前のファイルをコンテナにindex.htmlとしてコピーします。

```
docker cp /tmp/index02.html web02:/usr/local/apache2/htdocs/index.html
```

![](/images/sankaku17/10.png)
*「Sucessfully」と出ていることから、コピーが成功していることが確認できる*  

ブラウザで[http://Dockerホスト:8081/](http://localhost:8081/)に接続すると、コピーしたindex02.htmlの内容である「It's web02!」の表示が確認できました。

![](/images/sankaku17/11.png) 

#### 6.カレントディレクトリを戻す

[`pushd`コマンドを使ってディレクトリを移動している](https://zenn.dev/joho0724/articles/sankaku0724-newcreate17#1.%2Ftmp%E3%83%87%E3%82%A3%E3%83%AC%E3%82%AF%E3%83%88%E3%83%AA%E3%81%ABindex.html%E3%82%92%E4%BD%9C%E6%88%90%E3%81%99%E3%82%8B)ので、以下のコマンドで元のディレクトリに戻ります。

```
popd
```

![](/images/sankaku17/12.png)

### コンテナを作り直してファイルの中身を確認する

先ほどの作業を完了させた段階でコンテナの状態を確認すると、web01とweb02の二つのコンテナが存在していることがわかります。

![](/images/sankaku17/13.png)  

#### 1.コンテナの停止

ここで`docker stop`でweb01を停止させると、ブラウザで[http://Dockerホスト:8080/](http://localhost:8080/)に接続できなくなります。

![](/images/sankaku17/14.png)  

#### 2.コンテナの再稼働

また、`docker start`でweb01を再稼働させると、ブラウザで[http://Dockerホスト:8080/](http://localhost:8080/)に接続できるようになります。

![](/images/sankaku17/15.png)  

![](/images/sankaku17/16.png)
*接続できるように*

#### 3.コンテナの破棄

ここで、コンテナを破棄してみます。`docker stop`でweb01を停止させ、`docker rm`で削除します。

![](/images/sankaku17/17.png)  

確認すると、コンテナは完全に削除されていました。

![](/images/sankaku17/18.png)
*web01が存在していない様子*  

#### 4.コンテナの再構築

破棄したweb01コンテナを作り直してみます。以下のコマンドを実行し、その後`docker ps`で確認します。

```
docker run -dit --name web01 -p 8080:80 httpd:2.4
```

![](/images/sankaku17/19.png)  

この状態でブラウザから[http://Dockerホスト:8080/](http://localhost:8080/)に接続すると、「It's web01!」と表示されるのではなく「It works!」と表示されました。

![](/images/sankaku17/20.png)  

#### 5.コンテナの内部に入って確認してみる

web01コンテナの内部に入って、`cat`コマンドでindex.htmlの内容を確認すると「It works！」となっていることがわかります。

![](/images/sankaku17/21.png)  

このように、**同じ名前でコンテナを起動し直したとしても中身の内容は失われる**ということがわかります。
コンテナの破棄は慎重に行う必要があるということですね。

## 実際にバインドマウントをやってみた

`docker rm`でコンテナを破棄すると、そのコンテナの中にあるデータは失われます。しかし、コンテナで失いたくないデータをコンテナの外に出すことができればその問題は解決します。

そのような場合に役に立つのが**マウント**です。マウントを行うことでコンテナが削除されてもデータは失われず、永続化することができます。
この記事では、バインドマウントを行います。

#### 1.コンテナの停止と破棄

先ほどの作業の続きからバインドマウントをしていきます。

まず、web01コンテナを停止して破棄します。

![](/images/sankaku17/22.png)  

#### 2.マウントするディレクトリの作成をする

私は、`/Users/[私の名前]`ディレクトリに「web01data」というディレクトリを作成しました。

```
mkdir web01data
```

![](/images/sankaku17/23.png)
*web01dataディレクトリの作成*  

また、Dockerがホストシステムのディレクトリにアクセスできるようにするためには、ホストシステム上のディレクトリをDocker Desktopの設定で共有パスとして追加する必要があります。

Docker Desktopの設定を開き、「Settings」を選択します。

![](/images/sankaku17/28.png)

そこからFile Sharingに移動し、「+」ボタンをクリックして新しいディレクトリを追加します。

![](/images/sankaku17/24.png)  

この手順を完了することで、Dockerは`/Users/[私の名前]/web01data`ディレクトリにアクセスし、コンテナにマウントできるようになります。

#### 3.index.htmlを作成する

web0data内に以下のような内容のindex.htmlを配置します。

```html:index.html
<html>
<body>
<div>mount test</div>
</body>
<html>
```

#### 4.ディレクトリをバインドマウントしてweb01コンテナを起動する

「web01data」ディレクトリを`/usr/local/apache2/htdocs`にマウントしてweb01コンテナを起動します。

```
docker run -dit --name web01 -v /Users/[私の名前]/web01data:/usr/local/apache2/htdocs -p 8080:80 httpd:2.4
```

![](/images/sankaku17/25.png)  

`docker ps`で確認すると、無事にコンテナが起動していることがわかります。

![](/images/sankaku17/26.png)  


#### 5.ブラウザで確認してみる

![](/images/sankaku17/27.png)

この状態でブラウザから[http://Dockerホスト:8080/](http://localhost:8080/)に接続すると、「mount test」と表示されました。

また、**このコンテナを破棄して作り直してもindex.htmlは失われず、同じように表示されます。**

このように**マウントすることで、データの永続性を確保しつつ、コンテナの利便性や移植性を保つことができます。**

## バインドマウントの利点

ここまでの例では、コンテナ側からマウントしたファイルを書き換えませんでしたが、もちろん書き換えることもできます。書き換えた場合、そのデータはそのまま残ります。このようにバインドマウントを使用することでデータは失われません。間違えて`docker rm`で破棄しても影響を受けません。これは**コンテナのアップデートや差し替えが容易になる**ことを意味します。

また、マウントの手法は、データを失わないようにするだけでなく、**コンテナ間でのデータ共有にも利用できます**。1つの場所を2つ以上のコンテナで同時にマウントすることで、コンテナ間でファイルを共有できます。

Dockerでは、コンテナ内の設定ファイルを書き換えるために特定のフォルダやファイルをマウントすることもあります。例えば、httpdコンテナでは`/usr/local/apache2/conf`ディレクトリにApacheの設定ファイルがあり、設定を変更したいときはこのファイルを書き換えます。マウントを使用すれば、Dockerホスト上の適当なディレクトリに同じ内容のものを用意し、そのファイルを書き換えた上で`docker run`する際にマウントするといったことが可能になります。
この方法なら、**Dockerホストに設定ファイルが残るので、設定のバックアップが容易**です。

## さいごに

ざっくりまとめてみます。

**1. コンテナを破棄するとデータも破棄される**
コンテナは隔離された実行環境にすぎません。`docker rm`でコンテナを削除すると、データは失われます。

**2. データを永続化したい場合はマウントすると良い**
コンテナを破棄してもデータを残したい場合は、保存先をDockerホストのディレクトリなどの外に出してマウントすることで、失われないようにします。

**3. バインドマウント**
バインドマウントとは、Dockerホストのディレクトリをマウントする方法です。

ここまで記事を読んでくださり、ありがとうございました！今回は、私がDockerを利用していく中で重要だと感じたバインドマウントについてまとめてみました。

今後も、Dockerを理解するために突き進んでいきたいと思います！

**皆さんも素敵なハッピーDockerライフを！！！🌸**
