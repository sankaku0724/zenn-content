---
title: "Dockerコンテナ&イメージ削除方法"
emoji: "😸"
type: "idea"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "container"
  - "初心者"
published: false
---

## はじめに

この記事は、Dockerの初心者の私がコンテナとイメージの削除方法についてまとめたものです。

:::message
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- Docker Desktop 4.21.1 (114176)
- Docker Engine 24.0.2
- Docker Compose v2.19.1
- MacOS Sonoma 14.4.1

## Dockerコマンド一覧

Dockerのコマンド一覧は、以下のサイトに記載されています。このサイトは、Docker公式ドキュメントを有志の方々が日本語に翻訳してくれているものです。

https://docs.docker.jp/engine/reference/commandline/index.html

公式による最新のドキュメントを確認したい人は、以下のリンクから飛ぶことができます。

https://docs.docker.com/

## 一つのものを指定して削除する方法

### 一つのコンテナを指定して削除

コンテナを削除する時には、以下のように`docker rm`コマンドを用いましょう。
:::message
コンテナの破棄は、コンテナが停止している状態の時に行う必要があるため、**必ず`docker stop`コマンドを実行した後に`docker rm`コマンドを実行しなければなりません。**
:::

```
docker rm コンテナ名orコンテナID
```
### 一つのイメージを指定して削除

イメージを削除する時には、以下のように`docker image rm`コマンドを用いましょう。

```
docker image rm イメージ名orイメージID
```

## 全て指定して削除する方法

大量にコンテナやイメージを作成した後、不要になったものを一つ一つ削除するのはめんどくさいですよね。
そんな時には[**`prune`コマンド**](https://docs.docker.jp/config/pruning.html)を使いましょう。

### 全てのコンテナを削除

`docker container prune`と入力すれば、停止している全てのコンテナをまとめて削除できます。本当に削除してよいのか尋ねられた際は、「y」を入力すると削除できます。

```
docker container prune
```

![](/images/sankaku15/4.png)

### 全てのイメージを削除

イメージについても同様です。`docker image prune`と入力すれば、**どのコンテナも使っていない全てのイメージを削除**できます。

```
docker image prune
```

**既存のコンテナで使用されているイメージも消したい場合**には「-a」オプションをつけて実行すると削除することができます。

```
docker image prune -a
```

![](/images/sankaku15/5.png)
*どのコンテナも使っていないイメージが存在せず、既存のコンテナで使用されているイメージのみ存在する場合の実行結果*


### おまけ

Docker Desktopを使っている場合は、ダッシュボード上で簡単にコンテナやイメージの削除ができます。

![](/images/sankaku15/2.png)

コマンド入力をする必要がないのでとても楽で愛用しています！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

今回は、私がDockerを利用していく中で重要だと感じたDockerコンテナやイメージの削除方法についてまとめてみました。

不要なものは削除して、快適にDockerを扱っていきましょう！

**皆さんも素敵なハッピーDockerライフを！！！🌸**
