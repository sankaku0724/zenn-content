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
published: true
---

## はじめに

この記事では、Dockerのボリュームマウントとバックアップ方法について説明します。

:::message
Dockerのバインドマウントに関しては、私が以前投稿した[【初学者向け】Dockerバインドマウントの基礎と実践](https://zenn.dev/joho0724/articles/sankaku0724-newcreate17)という記事を見ていただけると幸いです。
また、もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
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

ここで、確保した領域のことを[**ボリューム**](https://docs.docker.jp/storage/volumes.html#use-volumes)といいます。

ボリュームマウントの利点は、ボリュームの保存場所がDocker Engineで管理されるため、その物理的な位置を意識する必要がないという点が挙げられます。例えば、ディレクトリ名を指定してバインドマウントを行う場合、ディレクトリ構造はDockerホストの構成によって違うので、それぞれの環境に合わせた場所を指定しなければならず、汎用性に欠けてしまいます。

それに対して、ボリュームを扱う方法はそれぞれの用途に対応したコマンドが存在しており、これはどのDockerホストでも同じように扱うことができるため、汎用的で扱いやすいという特徴があります。

## ボリュームマウントを行う

実際にボリュームマウントを行ってみます。また、今回は[**MySQL**](https://www.mysql.com/jp/)を使用し、データベースを扱うことにします。

DockerにおけるMySQLの使い方は、以下のサイトから確認できます。

https://hub.docker.com/_/mysql

#### 1. ボリュームを作成する

まず、ボリュームを作成します。ボリュームを作成するためには[**`docker volume create`**](https://docs.docker.jp/engine/reference/commandline/volume_create.html)コマンドを使用します。`docker volume create`コマンドは、

```
docker volume create ボリューム名
```

といった書式で使用できます。

ここでは、「`mysqlvolume`」という名前で作成してみます。

![](/images/sankaku18/1.png)

`docker volume ls`を用いると、存在するボリュームを確認することができます。

```
docker volume ls
```

![](/images/sankaku18/2.png)
*作成した「mysqlvolume」が確認できる*

#### 2. ボリュームマウントしたコンテナを作成する

`docker run`コマンドを用いて、MySQLのコンテナを起動してみます。
ここでは`db01`というコンテナ名にし、作成した`mysqlvolume`ボリュームを`/var/lib/mysql`ディレクトリにマウントします。また、eオプションを用いて、rootユーザーのパスワードを`MYSQL_ROOT_PASSWORD`として設定することでMySQLを起動します。

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

作成したコンテナの内部に入り、データベースを作成してデータを追加してみます。実行中のコンテナの中を操作するには[`docker exec`コマンドを使えば良い](https://zenn.dev/joho0724/articles/sankaku0724-newcreate12#%E5%AE%9F%E8%A1%8C%E4%B8%AD%E3%81%AE%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%A7%E3%82%B7%E3%82%A7%E3%83%AB%E3%82%92%E5%AE%9F%E8%A1%8C%E3%81%99%E3%82%8B)ので、それを用いてdb01コンテナの内部に入ってみます。

内部に入ったら、[`mysql`](https://dev.mysql.com/doc/refman/8.0/ja/mysql.html)コマンドを「-p」オプションをつけて実行します。ここで、-pオプションはパスワードの入力を求めるためのオプションです。パスワードが求められたら、コンテナを起動する際に`MYSQL_ROOT_PASSWORD`環境変数で設定したパスワードである「mypassword」を入力します。これにより、MySQLサーバーにrootユーザーとしてログインできます。rootユーザーはMySQLの最高権限を持つユーザーなので、データベースの作成や削除、テーブルの操作などができます。

![](/images/sankaku18/5.png)
*「mysql>」と表示され、MySQLの操作ができるように*  

#### 4. データベースを作成する

操作ができるようになったら、以下のコマンドで「`exampledb`」という名前のデータベースを作成してみます。

```
CREATE DATABASE exampledb;
```

![](/images/sankaku18/6.png)

#### 5. データベースのテーブルを作成する

まず、MySQLで使用するデータベースを`exampledb`に切り替えるために、以下のコマンドを実行します。

```
use exampledb;
```

これにより、現在のセッションで使用するデータベースが変更され、これ以降に実行するSQLコマンドは全て`exampledb`データベースに対して行われるようになります。

データベースを`exampledb`に切り替えたら、以下のコマンドでデータベース内に「`exampletable`」というテーブルを作成します。

```
CREATE TABLE exampletable (id INT NOT NULL AUTO_INCREMENT, name VARCHAR(50), PRIMARY KEY(id));
```

これにより、`exampletable`という名前の新しいテーブルが`exampledb`データベース内に作成されます。このテーブルには`id`と`name`という2つのカラムがあり、`id`カラムは自動的に増加する一意の整数値を持ち、主キーとして機能します。また、`name`カラムには最大50文字の文字列を格納することができるようになっています。

![](/images/sankaku18/7.png)

#### 6. データを挿入する

試しに「`user01`」と「`user02`」という二つのデータを挿入します。データは以下のようにして挿入することができます。

```
INSERT INTO テーブル名 (カラム) VALUES (値);
```

![](/images/sankaku18/8.png)
*二つのデータを挿入*

#### 7. データを確認する

挿入したデータを確認してみます。データは以下のようにして確認することができます。

```
SELECT * FROM テーブル名;
```

![](/images/sankaku18/9.png)
*データの挿入ができていることが確認できる*

#### 8. mysqlコマンドを終了してコンテナの外に出る

データの挿入ができていることが確認できたので、mysqlコマンドを終了してコンテナの外に出ます。mysqlコマンドを終了するには以下のように入力します。

```
\q
```

終了したら`exit`でコンテナの外に出ます。

![](/images/sankaku18/10.png)

#### 9. コンテナを破棄する

`docker stop`コマンドと`docker rm`コマンドでコンテナを破棄します。

![](/images/sankaku18/11.png)

`docker ps`コマンドで確認すると、コンテナが破棄されていることが確認できました。

![](/images/sankaku18/12.png)

#### 10. 新しいコンテナをマウントせずに作成

ここで、-vオプションを指定せず、新しいコンテナをマウントせずに作成します。

```
docker run --name db01 -dit -e MYSQL_R00T_PASSWORD=mypassword mysql
```

![](/images/sankaku18/13.png)

コンテナを作成したら、先ほどと同様に`docker exec`コマンドでシェルに入り、mysqlコマンドを実行します。
使用するデータベースを`exampledb`に切り替えようとすると、そのようなデータベースは存在しないというエラーが出ました。

![](/images/sankaku18/14.png)
*エラー発生*

**データが存在しないことが確認できた**ので、mysqlコマンドを終了してコンテナの外に出ます。

![](/images/sankaku18/15.png)

#### 11. コンテナを破棄し、マウントした新しいコンテナを作成

先ほどのコンテナを破棄し、新しくマウントしたコンテナを作成します。

```
docker run --name db01 -dit -v mysqlvolume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=mypassword mysql
```

![](/images/sankaku18/16.png)

コンテナを作成したら、先ほどと同様に`docker exec`コマンドでシェルに入り、mysqlコマンドを実行します。
使用するデータベースを変更しようとすると、今回は`exampledb`に正しく切り替えられたことが確認できました。

![](/images/sankaku18/17.png)

データを確認すると、挿入したデータが存在していることがわかります。

![](/images/sankaku18/18.png)

**データが存在していることが確認できた**ので、mysqlコマンドを終了し、コンテナの外に出てからコンテナを破棄します。

![](/images/sankaku18/19.png)

このように、**ボリュームを用いたマウントをすることによって、データは失われることなく保持される**ということが確認できました。

## データのバックアップを行う

コンテナを扱う場合、データのバックアップについて考慮する必要があります。
バインドマウントの場合は、Dockerホスト上のファイルに直接アクセスできるため、バックアップを簡単に行うことができます。

では、ボリュームマウントの場合はどうすればよいのでしょうか。
先ほどの手順では、MySQLコンテナのデータを`mysqlvolume`という名前のボリュームに保存しました。もし、このボリュームが失われれば、データベースのデータも失われてしまいます。つまり、**ボリュームの内容さえ保存することができれば、バックアップを取ることができる**ということになります。

#### 1. ボリュームを確認する

ボリュームの情報は[**`docker volume inspect`**](https://docs.docker.jp/engine/reference/commandline/volume_inspect.html)で確認することができます。

```
docker volume inspect mysqlvolume
```

![](/images/sankaku18/20.png)
*ボリュームの詳細情報*

Mountpoint(マウントされている場所)を見ると、「`/var/lib/docker/volumes/`」以下にあることが示されています。

#### 2. ボリュームをバックアップする

しかし、MountpointはDocker Engineのシステム領域であり、ここをバックアップしてリストア（バックアップからデータを元の状態に戻すこと）しても、元に戻るとは限りません。

そこで、Dockerでボリュームをバックアップする際には、適当なコンテナにボリュームを割り当て、そのコンテナを使ってバックアップを取るという方法があります。例えば、Linuxシステムが入ったコンテナを別に1つ起動し、そのディレクトリにバックアップ対象のコンテナをマウントするという方法です。次に、`tar`コマンドなどを使ってバックアップを作成し、そのバックアップをDockerホストに取り出すことでバックアップは完了します。リストアする際には、逆の手順でデータを戻すことができます。

今回は、軽量Linuxシステムの`busybox`が入ったコンテナを`mysqlvolume`をマウントして起動します。そして、`tar`コマンドでバックアップするようにもします。以下のコマンドにより、`mysqlvolume`の内容が`backup.tar.gz`としてカレントディレクトリに保存されます。

```
docker run --rm -v mysqlvolume:/src -v "$PWD":/dest busybox tar czvf /dest/backup.tar.gz -C /src .
```

![](/images/sankaku18/21.png)

実行すると、カレントディレクトリに`backup.tar.gz`としてバックアップが作成できました。

![](/images/sankaku18/22.png)

バックアップを保存できたので、ボリュームを削除します。ボリュームを削除する際は、[**`docker volume rm`**](https://docs.docker.jp/engine/reference/commandline/volume_rm.html)を用います。

```
docker volume rm mysqlvolume
```

`docker volume ls`で確認すると、ボリュームが削除されていることが確認できました。

![](/images/sankaku18/23.png)


:::details コンテナをバックアップ対象にする
先ほどは、ボリュームをバックアップ対象にしました。しかし、コンテナ数が増えてきた場合や複数の管理者がコンテナを管理する場合には、それぞれのコンテナがどのボリュームを使っているのかを洗い出してバックアップするのは手間がかかってしまいます。

そのような場合には[**`--volumes-from`**](https://docs.docker.jp/v1.11/engine/userguide/containers/dockervolumes.html#id9)オプションを用いてコンテナをリストア対象にしてバックアップを行うという方法があります。

`--volumes-from`オプションを用いることで、コンテナを起動する際に別のコンテナのマウント情報を引き継ぎ、同じ設定でマウントすることができます。
例えば、以下のようにコマンドを実行した場合、先ほどと同様に`backup.tar.gz`としてカレントディレクトリにバックアップを保存することができます。

```
docker run --rm --volumes-from db01 -v "$PWD":/dest busybox tar czf /dest/backup.tar.gz -C /var/lib/mysql .
```

この方法の利点は、バックアップ対象をボリューム名ではなく、そのコンテナのディレクトリ名で指定できるという点です。コンテナのディレクトリが、どのボリュームにマウントされているかを意識する必要がありません。
また、上記の例では1つしかボリュームをマウントしていませんが、複数のボリュームをマウントしている場面では、一括マウントができるのも便利なポイントです。
:::

## データのリストアを行う

#### 1. ボリュームをリストアする

では、保存したバックアップを利用して、ボリュームをリストアしてみます。まず、ボリュームを作成します。

```
docker volume create mysqlvolume
```

作成したら、以下のコマンドを実行することでホストの現在のディレクトリにある`backup.tar.gz`ファイルの内容が`mysqlvolume`ボリュームにリストアされます。

```
docker run --rm -v mysqlvolume:/dest -v "$PWD":/src busybox tar xzf /src/backup.tar.gz -C /dest
```

![](/images/sankaku18/24.png)

ここで、[先ほど説明した手順](https://zenn.dev/joho0724/articles/sankaku0724-newcreate18#%E3%83%9C%E3%83%AA%E3%83%A5%E3%83%BC%E3%83%A0%E3%83%9E%E3%82%A6%E3%83%B3%E3%83%88%E3%82%92%E8%A1%8C%E3%81%86)と同様にして、リストアが成功していることが確認できました。

![](/images/sankaku18/25.png)
*データベースの内容が一致している*

これにより、ボリュームマウントした際のバックアップの保存とそれを利用したリストアの方法について確認できました。

## バインドマウントとボリュームマウントの使い分け

汎用性の面ではボリュームマウントに軍配が上がりますが、バインドマウントのほうが優れた場面もあります。

**バインドマウントの利点は、Dockerホストの物理的な位置にマウントできることです**。例えば、Dockerホスト上に設定ファイルを置いたディレクトリを用意して、それをコンテナに渡したい場合や作業ディレクトリの変更を即座にDockerコンテナから参照したい場合に役立ちます。個人のPCでマウントを扱う場合は、バインドマウントを用いると良いかもしれません。

逆に**ボリュームマウントは、Dockerホストから不用意にデータを書き換えたくない場面に向いています**。例えば、データベースを構成するコンテナにおいて、データを保存する際にそれぞれのファイルをDockerホストから編集することはないはずです。

## さいごに

ざっくりまとめてみます。

**1. バインドマウントとボリュームマウント**
マウントには、バインドマウントとボリュームマウントというものがあります。前者は、Dockerホストのディレクトリをマウントする方法、後者はDocker Engineで管理されている領域にマウントするものです。

**2. バックアップ**
データのバックアップは、マウント先を`tar.gz`などでアーカイブします。ボリュームマウントの場合は、対象をマウントするコンテナを作り、そのコンテナ内で`tar`コマンドなどを実行してバックアップをとって必要なデータを取り出します。

ここまで記事を読んでくださり、ありがとうございました！今回は、私がDockerを利用していく中で重要だと感じたボリュームマウントとそのバックアップ方法についてまとめてみました。

今後も、Dockerを理解するために突き進んでいきたいと思います！

**皆さんも素敵なハッピーDockerライフを！！！🌸**
