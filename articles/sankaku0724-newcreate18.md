---
title: "【初学者向け】Dockerボリュームマウントとバックアップの基礎と実践"
emoji: "🐬"
type: "tech"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "container"
  - "mysql"
published: false
---

## はじめに

この記事では、Dockerのボリュームマウントについて説明します。

:::message
Dockerのバインドマウントに関しては，私が以前投稿した[【初学者向け】Dockerバインドマウントの基礎と実践](https://zenn.dev/joho0724/articles/sankaku0724-newcreate17)という記事を見ていただけると幸いです．
また，もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
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

## ボリュームマウントって何？

[**Dockerのボリュームマウント**](https://docs.docker.jp/storage/volumes.html#id5)は、Docker Engine上で確保した領域をマウントするための機能です。

ここで，確保した領域のことを[**ボリューム**](https://docs.docker.jp/storage/volumes.html#use-volumes)といいます．

ボリュームマウントの利点は、ボリュームの保存場所がDocker Engineで管理されるため、その物理的な位置を意識する必要がないという点が挙げられます。例えば，ディレクトリ名を指定してバインドマウントを行う場合，ディレクトリ構造はDockerホストの構成によって違うので、それぞれの環境に合わせた場所を指定しなければならず、汎用性に欠けてしまいます。

それに対して、ボリュームを扱う方法はそれぞれの用途に対応したコマンドが存在しており、これはどのDockerホストでも同じように扱うことができるため，汎用的で扱いやすいという特徴があります。

## ボリュームマウントを行う

実際にボリュームマウントを行ってみます．また，今回は[**MySQL**](https://www.mysql.com/jp/)を使用し，データベースを扱うことにします．

DockerにおけるMySQLの使い方は，以下のサイトから確認できます。

https://hub.docker.com/_/mysql

#### 1. ボリュームを作成する

まず，ボリュームを作成します．ボリュームを作成するためには[**`docker volume create`**](https://docs.docker.jp/engine/reference/commandline/volume_create.html)コマンドを使用します．`docker volume create`コマンドは、

```
docker volume create ボリューム名
```

といった書式で使用できます。

ここでは，「mysqlvolume」という名前で作成してみます．

![](/images/sankaku18/1.png)

`docker volume`コマンドを用いると，存在するボリュームを確認することができます．

```
docker volume ls
```

![](/images/sankaku18/2.png)
*作成した「mysqlvolume」が確認できる*

#### 2. ボリュームマウントしたコンテナを作成する

`docker run`コマンドを用いて，MySQLのコンテナを起動してみます．
ここではdb01というコンテナ名にし，作成したmysqlvolumeボリュームを`/var/lib/mysql`ディレクトリにマウントします。また，eオプションを用いて，rootユーザーのパスワードを`MYSQL_ROOT_PASSWORD`として設定することでMySQLを起動します．

```
docker run --name db01 -dit -v mysqlvolume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypassword mysql
```

![](/images/sankaku18/3.png)

`docker ps`コマンドで確認すると、コンテナの起動が確認できました。

![](/images/sankaku18/4.png)

:::message
`docker ps`コマンドで「-a」オプションを指定すると、稼働中ではないものも含むすべてのコンテナ一覧を確認することができます。
:::

#### 3. コンテナの内部に入って操作する

作成したコンテナの内部に入り，データベースを作成してデータを追加してみます．実行中のコンテナの中を操作するには[`docker exec`コマンドを使えば良い](https://zenn.dev/joho0724/articles/sankaku0724-newcreate12#%E5%AE%9F%E8%A1%8C%E4%B8%AD%E3%81%AE%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%A7%E3%82%B7%E3%82%A7%E3%83%AB%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B)ので、それを用いてdb01コンテナの内部に入ってみます。

内部に入ったら，[`mysql`](https://dev.mysql.com/doc/refman/8.0/ja/mysql.html)コマンドを「-p」オプションをつけて実行します．ここで，-pオプションはパスワードの入力を求めるためのオプションです．パスワードが求められたら、コンテナを起動する際に`MYSQL_ROOT_PASSWORD`環境変数で設定したパスワードである「mypassword」を入力します。これにより，MySQLサーバーにrootユーザーとしてログインできます。rootユーザーはMySQLの最高権限を持つユーザーなので、データベースの作成や削除、テーブルの操作などができます。

![](/images/sankaku18/5.png)
*「mysql」と表示され、MySQLの操作ができるように*  

#### 4. データベースを作成する

操作ができるようになったら，以下のコマンドで「exampledb」という名前のデータベースを作成してみます．

```
CREATE DATABASE exampledb;
```

![](/images/sankaku18/6.png)

#### 5. データベースのテーブルを作成する

まず，MySQLで現在使用するデータベースをexampledbに切り替えるために，以下のコマンドを実行します．

```
use exampledb;
```

これにより，現在のセッションで使用するデータベースが変更され，これ以降に実行するSQLコマンドは全て`exampledb`データベースに対して行われるようになります．

データベースをexampledbに切り替えたら，以下のコマンドでデータベース内に「exampletable」というテーブルを作成します．

```
CREATE TABLE exampletable (id INT NOT NULL AUTO_INCREMENT, name VARCHAR(50), PRIMARY KEY(id));
```

これにより，exampletable という名前の新しいテーブルが exampledb データベース内に作成されます。このテーブルには id と name という2つのカラムがあり、id カラムは自動的に増加する一意の整数値を持ち、主キーとして機能します。また，name カラムには最大50文字の文字列を格納することができるようになっています。

![](/images/sankaku18/7.png)

#### 6. データを挿入する

![](/images/sankaku18/8.png)

#### 7. データを確認する

```
SELECT * FROM exampletable;
```

![](/images/sankaku18/9.png)

#### 8. 

![](/images/sankaku18/10.png)

![](/images/sankaku18/11.png)

![](/images/sankaku18/12.png)

![](/images/sankaku18/13.png)

![](/images/sankaku18/14.png)

![](/images/sankaku18/15.png)

![](/images/sankaku18/16.png)

![](/images/sankaku18/17.png)

![](/images/sankaku18/18.png)

![](/images/sankaku18/19.png)

## データのバックアップを行う


![](/images/sankaku18/20.png)

![](/images/sankaku18/21.png)

![](/images/sankaku18/22.png)

![](/images/sankaku18/23.png)

![](/images/sankaku18/24.png)

![](/images/sankaku18/25.png)

![](/images/sankaku18/26.png)


## バインドマウントとボリュームマウントの使い分け

汎用性の面で言うとボリュームマウントに軍配が上がりますが、バインドマウントのほうが優れた場面もあります。

**バインドマウントの利点は、Dockerホストの物理的な位置にマウントできることです**。例えば，Dockerホスト上に設定ファイルを置いたディレクトリを用意して、それをコンテナに渡したい場合や作業ディレクトリの変更を即座にDockerコンテナから参照したい場合に役立ちます．個人のPCでマウントを扱う場合は，バインドマウントを用いると良いかもしれません．

逆に**ボリュームマウントは、Dockerホストから不用意にデータを書き換えたくない場面に向いています**．例えば、データベースを構成するコンテナにおいて、データを保存する際にそれぞれのファイルをDockerホストから編集することはないはずです。

## さいごに

ざっくりまとめてみます。

**1. バインドマウントとボリュームマウント**
マウントには、バインドマウントとボリュームマウントというものがあります。前者は、Dockerホストのディレクトリをマウントする方法、後者はDocker Engineで管理されている領域にマウントするものです。

**2. バックアップ**
データのバックアップは、マウント先をtar.gzなどでアーカイブします。ボリュームマウントの場合は、対象をマウントするコンテナを作り、そのコンテナ内でtarコマンドなどを実行してバックアップをとって必要なデータを取り出します。

ここまで記事を読んでくださり、ありがとうございました！今回は、私がDockerを利用していく中で重要だと感じたボリュームマウントについてまとめてみました。

今後も、Dockerを理解するために突き進んでいきたいと思います！

**皆さんも素敵なハッピーDockerライフを！！！🌸**
