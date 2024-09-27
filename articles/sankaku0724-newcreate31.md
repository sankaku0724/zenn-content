---
title: "【初学者向け】Dockerfileからイメージを作成する"
emoji: "🔧"
type: "tech"
topics:
  - "dockerdesktop"
  - "入門"
  - "docker"
  - "dockerfile"
  - "python"
published: false
---

## はじめに

今回は私が**Dockerfileからイメージを作成**した際の話をまとめたものになります。

:::message
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- Docker Desktop 4.34.2 (167172)
- Docker Engine 27.2.0
- Dockerfile v2.29.2-desktop.2
- MacOS Sonoma 14.6.1

## Dockerコマンド一覧

Dockerのコマンド一覧は、以下のサイトに記載されています。このサイトは、Docker公式ドキュメントを有志の方々が日本語に翻訳してくれているものです。

https://docs.docker.jp/engine/reference/commandline/index.html

公式による最新のドキュメントを確認したい人は、以下のリンクから飛ぶことができます。

https://docs.docker.com/

## Dockerfileとは？

[**Dockerfile**](https://docs.docker.jp/engine/reference/builder.html)は、Dockerイメージを作成するための設計図や手順書となるテキストファイルです。
**同じDockerfileから常に同じ環境を構築できる**ことがメリットです。これにより、開発者は一貫性のある再現可能な環境を簡単に作成し、共有することができます。

Dockerfileは主に以下のような命令で構成することができます。

| 主な命令      | 説明                                  |
|-----------|-----------------------------------------|
| FROM      | ベースイメージを指定                  |
| RUN       | コマンドを実行                        |
| CMD       | コンテナ実行時のデフォルトコマンドを設定 |
| LABEL     | イメージにメタデータを追加            |
| EXPOSE    | コンテナがリッスンするポートを指定     |
| ENV       | 環境変数を設定                        |
| ADD       | ファイルやディレクトリをイメージに追加  |
| COPY      | ファイルやディレクトリをイメージにコピー|
| ENTRYPOINT| コンテナ実行時のメインコマンドを設定   |
| VOLUME    | ボリュームマウントポイントを作成      |
| USER      | コマンド実行時のユーザーを指定        |
| WORKDIR   | 作業ディレクトリを設定                |
| ARG         | ビルド時の変数を定義                  |
| ONBUILD     | 子イメージのビルド時に実行される命令を追加|
| HEALTHCHECK | コンテナのヘルスチェック方法を定義     |
| SHELL       | デフォルトシェルを変更                |

## Dockerfileを使ってみる

実際にDockerfileを使って、「Hello, Docker!」と出力するシンプルなPythonプログラムを実行してみます。

### 1. 作業用ディレクトリを作成する

「my-python-app」という作業用ディレクトリを作成し、移動します。

```
mkdir my-python-app
cd my-python-app
```

![](/images/sankaku31/1.png)

### 2. Pythonスクリプトを作成する

まず、以下のコマンドで「`app.py`」というタグのPythonスクリプトを作成します。

```
touch app.py
```

![](/images/sankaku31/2.png)

作成したら、以下のように記述します。

```py:app.py
print("Hello, Docker!")
```

### 3. Dockerfileを作成する

次に、以下のコマンドでDockerfileを作成します。

```
touch Dockerfile
```

![](/images/sankaku31/3.png)

作成したら、以下のように記述します。

```Dockerfile:Dockerfile
# ベースイメージとして公式のPythonイメージを使用
FROM python:3.9-slim

# 作業ディレクトリを設定
WORKDIR /app

# ローカルのapp.pyをコンテナ内にコピー
COPY app.py /app

# 実行するコマンドを指定
CMD ["python", "app.py"]
```

:::details Dockerfileの構成
1. `Python 3.9`の`slim`（軽量）バージョンをベースイメージとして使用
2. コンテナ内の作業ディレクトリを`/app`に設定
3. ローカルの`app.py`ファイルをコンテナの`/app`ディレクトリにコピー
4. コンテナ起動時に`python app.py`コマンドを実行
:::

### 4. Dockerイメージをビルドする

Dockerfileがあるディレクトリで以下のコマンドを実行して、イメージをビルドします。

```
docker build -t my-python-app .
```

:::details コマンドの構成

- [`docker build`](https://docs.docker.jp/engine/reference/commandline/build.html)
この部分は、**Dockerイメージをビルド**するためのコマンドです。ここでいうビルドは作成の意味です。`Dockerfile`に書かれた指示に従って、イメージを構築します。

- `-t my-python-app`
`-t`は "tag" の略で、作成するDockerイメージにタグをつけるオプションです。今回は、作成するイメージに`my-python-app`というタグをつけています。タグをつけることで、後でイメージを参照しやすくなります。

- `.`（ドット）
ドットは、現在のディレクトリ（カレントディレクトリ）を意味します。つまり、`Dockerfile`が存在するディレクトリを指定していることになります。`docker build`コマンドは、このディレクトリ内の`Dockerfile`を使ってイメージをビルドします。

:::

![](/images/sankaku31/4.png)
*Dockerイメージをビルド*

以下のコマンドでイメージ一覧を確認してみます。

```
docker image ls
```

すると、`my-python-app`というイメージが作成できたのが確認できました。

![](/images/sankaku31/5.png)

また、あくまでも作成したのはイメージのため、コンテナは存在していません。

![](/images/sankaku31/6.png)

### 5. コンテナを実行する

では、以下のコマンドを実行し、`my-python-app`イメージのタグを指定してコンテナを起動します。

```
docker run my-python-app
```

すると、「Hello, Docker!」と出力されました！

![](/images/sankaku31/7.png)

コンテナを確認すると、新しいコンテナが作成されています。

![](/images/sankaku31/8.png)

これによって、**Dockerfileを使って「Hello, Docker!」と出力するシンプルなPythonプログラムを実行することができました！**

:::details docker runの注意点

では、もう一度`docker run my-python-app`を実行し、コンテナを確認してみます。
すると、以下のようになりました。

![](/images/sankaku31/9.png)
*二つのコンテナが存在している*

`docker run`コマンドは、指定したDockerイメージを元に新しいコンテナを作成して実行するため、**同じコマンドを何度も実行するとその度に新しいコンテナが作られてしまいます。**
何度も繰り返し実行する際には`--rm`をつけて、実行した後に自動でコンテナを破棄するなどの対策を取るのが無難でしょう。

```
docker run --rm my-python-app
```

![](/images/sankaku31/10.png)
*`--rm`で自動的にコンテナを破棄することができる*

:::

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

今回は、Dockerfileからイメージを作成する手順について紹介しました。

今回の記事で使用したファイルのリポジトリはこちらになります。

https://github.com/sankaku0724/my-python-app

私もまだまだ慣れていないため、今後も知識を養いながら、色々なことにチャレンジしてみたいです。

**皆さんも素敵なハッピーDockerライフを！！！🌸**
