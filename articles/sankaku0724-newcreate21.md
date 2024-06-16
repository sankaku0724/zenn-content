---
title: "【初学者向け】Dockerバインドマウントの基礎と実践"
emoji: "🫨"
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

今回は、Dockerのファイル操作をする方法について紹介します。

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


http://localhost:8080/

nano index.html

'''html
<html>
<body>
<div>It's web01!</div>
</body>
<html>
'''

docker cp /tmp/index.html web01:/usr/local/apache2/htdocs/


https://hub.docker.com/_/mysql

## さいごに

ここまで記事を読んでくださり、ありがとうございました！今回は、ZennのPV数をGoogle Analyticsで確認する方法について紹介しました。興味を持った方は、ぜひGoogle Analyticsを利用してZennの執筆のモチベーションを上げていきましょう！

**皆さんも素敵なハッピーDockerライフを！！！🌸**

