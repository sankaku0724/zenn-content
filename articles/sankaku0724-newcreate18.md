---
title: "【初学者向け】Dockerボリュームマウントの基礎と実践"
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
Dockerのバインドマウントについては，私が以前投稿した[【初学者向け】Dockerバインドマウントの基礎と実践](https://docs.docker.com/)という記事を見ていただけると幸いです．
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

## ボリュームマウントを行う

https://www.mysql.com/

https://www.mysql.com/jp/

https://hub.docker.com/_/mysql

![](/images/sankaku18/1.png)  

![](/images/sankaku18/2.png)  

![](/images/sankaku18/3.png)  

![](/images/sankaku18/4.png)  

![](/images/sankaku18/5.png)  

![](/images/sankaku18/6.png)  

![](/images/sankaku18/7.png)  

![](/images/sankaku18/8.png)  

![](/images/sankaku18/9.png)  

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

![](/images/sankaku18/20.png)  

![](/images/sankaku18/21.png)  

![](/images/sankaku18/22.png)  

![](/images/sankaku18/23.png)  

![](/images/sankaku18/24.png)  

![](/images/sankaku18/25.png)  

![](/images/sankaku18/26.png)  






## さいごに

（3） バインドマウントとボリュームマウント
マウントには、バインドマウントとボリュームマウントがあります。前者は、Dockerホストのディレクトリをマウントする方法、後者は、Docker Engineで管理されている領域にマウントするものです。
（4） バックアップ
データのバックアップは、マウント先をtargなどでアーカイブします。ボリュームマウントのときは、対象をマウントするコンテナを作り、そのコンテナ内でtarコマンドなどを実行してバックアップをとって取り出します。

ここまで記事を読んでくださり、ありがとうございました！今回は、ZennのPV数をGoogle Analyticsで確認する方法について紹介しました。興味を持った方は、ぜひGoogle Analyticsを利用してZennの執筆のモチベーションを上げていきましょう！

**皆さんも素敵なハッピーDockerライフを！！！🌸**

