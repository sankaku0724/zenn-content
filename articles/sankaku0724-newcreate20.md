---
title: "【初学者向け】Dockerネットワーク"
emoji: "🌐"
type: "tech"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "container"
  - "ネットワーク"
published: false
---

## はじめに

今回は、Dockerのネットワーク

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


```
apt update
apt -y upgrade
apt install -y iproute2 iputils-ping curl
```

ping -c 3 172.17.0.2

curl http://172.17.0.2/

docker run -dit --name web01 -p 8080:80 --net mynetwork httpd:2.4

```

```








![](/images/sankaku20/1.png)

![](/images/sankaku20/2.png)

![](/images/sankaku20/3.png)

![](/images/sankaku20/4.png)

![](/images/sankaku20/5.png)

![](/images/sankaku20/6.png)

![](/images/sankaku20/7.png)

![](/images/sankaku20/8.png)

![](/images/sankaku20/9.png)

![](/images/sankaku20/10.png)

![](/images/sankaku20/11.png)

![](/images/sankaku20/12.png)

![](/images/sankaku20/13.png)

![](/images/sankaku20/14.png)

![](/images/sankaku20/15.png)

![](/images/sankaku20/16.png)

![](/images/sankaku20/17.png)

![](/images/sankaku20/18.png)

![](/images/sankaku20/19.png)

![](/images/sankaku20/20.png)

![](/images/sankaku20/21.png)

![](/images/sankaku20/22.png)

![](/images/sankaku20/23.png)

![](/images/sankaku20/24.png)









## さいごに

ここまで記事を読んでくださり、ありがとうございました！今回は、ZennのPV数をGoogle Analyticsで確認する方法について紹介しました。興味を持った方は、ぜひGoogle Analyticsを利用してZennの執筆のモチベーションを上げていきましょう！

**皆さんも素敵なハッピーDockerライフを！！！🌸**

