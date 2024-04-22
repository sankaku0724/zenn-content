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

:::message
この記事は、この記事は，以前私が書いた「[【初学者向け】DockerでWebサーバーを起動する](https://zenn.dev/joho0724/articles/sankaku0724-newcreate10)」という記事の内容を踏まえて、Dockerの基本操作についてまとめたものです。
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

https://zenn.dev/joho0724/articles/sankaku0724-newcreate10
