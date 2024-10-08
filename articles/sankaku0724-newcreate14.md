---
title: "【golang】DockerでGo言語を導入してみた"
emoji: "🫨"
type: "tech"
topics:
  - "dockerdesktop"
  - "cloud"
  - "docker"
  - "プログラミング"
  - "go"
published: true
---

## はじめに

今回は、Dockerを用いてGo言語を導入する方法について紹介します。

:::message
この記事は、Docker&Go言語初心者である私の解釈に基づいてまとめたものです。
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- Docker Desktop 4.21.1 (114176)
- Docker Engine 24.0.2
- Docker Compose v2.19.1
- MacOS Sonoma 14.4.1

## 経緯（というほどのものでもないですが）

Dockerの練習がてら、何か環境を試してみたいなぁ⋯
せや！Go言語って有名だけど使ったことないし試してみるか！

~~もっと使い慣れた言語で試すべきでした。~~

## Go言語を扱う①

Dockerコンテナを使えば、プログラミング言語の環境を手軽に試せます。コンテナを破棄してしまえば、元の状態にすぐに戻せますしね。

### 1. ソースコードを用意する

私はカレントディレクトリに、以下のような「hello.go」というソースコードを用意しました。

```go:hello.go
package main

import "fmt"

func main(){
	fmt.Printf("Hello World\n")
}
```

このプログラムは実行すると、画面に「Hello World」と表示するようになっています。

### 2. Go言語のコンテナを起動して実行する

Go言語のイメージは、「golang」という名前です。下記のサイトに使い方が記述されています。

https://hub.docker.com/_/golang

記載されている使い方の通り（2024年5月時点）に次のコマンドを入力して実行してみます。

```
docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp golang:1.22 go build -v
```

ここで指定したオプションは、次の通りです。

- `--rm`

実行が完了した時に、このコンテナを破棄するオプションです。

- `-v "$PWD":/usr/src/myapp`

カレントディレクトリを、コンテナ内の/usr/src/myappに割り当てる役割を持っています。

- `-w /usr/src/myapp`

「-w」オプションは、コンテナ内のプログラムを実行する時の作業ディレクトリを指定します。/usr/src/myappは、vオプションでマウントしたディレクトリです。これはDockerホストのカレントディレクトリにマウントされているため、コンテナ内ではこのディレクトリに対してGo言語のビルドが実行されます。

![](/images/sankaku14/0.png)


ところが、コンテナを起動することができませんでした。エラーの内容を見ると、**go.modファイルが存在しない**と出ています。

[go.modについて説明している公式サイト](https://go.dev/doc/modules/gomod-ref)によると、どうやら、**go.modファイルはGo言語の依存関係を管理するために必要**であるようです。
調べてみると[Go言語の公式サイト](https://go.dev/doc/tutorial/create-module)に解決方法が記載されていました。

`go mod init`コマンドを使用することで、go.modファイルを作成できるようです。

`go mod init`コマンドは、

```
go mod init プロジェクト名
```

といった書式で使用できます。

ではこれをコンテナの中で使用しましょう。[コンテナの中をメンテナンスするためには「-it」オプションをつければ良い](https://zenn.dev/joho0724/articles/sankaku0724-newcreate12#2.%E3%82%B3%E3%83%B3%E3%83%86%E3%83%8A%E3%81%AE%E3%83%87%E3%82%BF%E3%83%83%E3%83%81%E3%81%A8%E3%82%A2%E3%82%BF%E3%83%83%E3%83%81)ので、それを付けてシェルで操作できるようにして実行してみます。

```
docker run --rm -it -v "$(pwd)":/go/src/app -w /go/src/app golang /bin/bash
```

内部を操作できるようになったら、以下のコマンドを実行してgo.modファイルを作成します。

```
go mod init myapp
```

また、今回はコンパイルもコンテナ内で行う必要があるため、以下のコマンドを実行してコンパイルしましょう。

```
go build -o myapp .
```
ここで指定したオプションは、次の通りです。

- `go build`

Go言語のコンパイラで、Goのソースコードをバイナリ実行ファイルに変換します。

- `-o myapp`

-oオプションは、出力ファイルの名前を指定します。ここではmyappという名前の実行可能ファイルを指定しています。

- `.`

ビルド対象を指定します。この場合、カレントディレクトリ内にある全てのGo言語のソースコードを対象としています。

ここで`./myapp`と入力すると実行できます。

```
./myapp
```

実行すると、画面には「`Hello World`」と表示されます。

作業が終わった後には`exit`でシェルを終了させるのも忘れずに。

```
exit
```

![](/images/sankaku14/1.png)
*実際の作業画面*

「--rm」オプションをつけているため、コンテナは終了した際に破棄されます。`docker ps -a`で確認すると、コンテナが存在しないことがわかります。

![](/images/sankaku14/3.png)

## 実行環境確認

先ほどの手順でGoのソースコードを実行できました。

上記の作業を行うと、カレントディレクトリに`myapp`という名前の実行可能ファイルと`go.mod`ファイルが作成されます。

![](/images/sankaku14/5.png)

ここで注意しなければならないのは、コンテナ内で作成した実行可能ファイルはコンテナの環境で実行できるように作られているため、**同じ環境を再現しなければ実行することができない**ということです。

![](/images/sankaku14/2.png)
*手元で実行するとフォーマットが違うというエラーが出るが、コンテナ内の環境であれば実行できる*

試しにfileコマンドでmyappのファイルタイプを判定してみると、以下のようになりました。

![](/images/sankaku14/6.png)

ここで、「ELF」と出力されています。これは[Linux環境で実行可能な形式](https://ja.wikipedia.org/wiki/%E5%AE%9F%E8%A1%8C%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB#%E5%AE%9F%E8%A1%8C%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E5%BD%A2%E5%BC%8F)であり、今回作成したmyappがmacOS環境で実行できなかった理由が判明しました。

## Go言語を扱う②

先ほどの実行方法では、せっかくコンテナ内で環境を構築するといったことをしているのに、手元に`myapp`や`go.mod`ファイルが残ってしまいます。

ファイルを作成せずにもっとより簡単にGoソースコードを実行する方法がないか調べていると、「[`go run`は指定されたメインGoパッケージをコンパイルして実行するが、`go build`は指定されたパッケージとその依存関係をコンパイルする](https://stackoverflow.com/questions/28755916/go-how-does-go-run-file-go-work)」という記述を見つけました。

つまり、[`go build`](https://pkg.go.dev/cmd/go#hdr-Compile_packages_and_dependencies)ではなく[`go run`](https://pkg.go.dev/cmd/go#hdr-Compile_and_run_Go_program)を使用すれば、依存関係を管理する役割を持つgo.modファイルを作成する必要がないようです。また、実行のためのコマンドを入力する手間も省けて一石二鳥です。

試しに以下のコマンドを実行してみました。このコマンドは、指定したGoのソースコード（先ほど用意したhello.go）を、golang:1.22イメージ内で実行するようになっています。

```
docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp golang:1.22 go run hello.go
```

実行すると、画面に「`Hello World`」と表示され、手元に`myapp`や`go.mod`ファイルが残るようなこともありませんでした。

![](/images/sankaku14/4.png)

## 結論

DockerでGo言語を扱う際には、以下のように実行する方法が存在するということがわかりました。

```
docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp golang go run [実行したいgoソースコード名]
```

このコマンドによって、Go言語のプログラムを[最新版](https://zenn.dev/joho0724/articles/sankaku0724-newcreate12#%E6%9C%80%E6%96%B0%E7%89%88%E3%82%92%E6%84%8F%E5%91%B3%E3%81%99%E3%82%8B%E3%80%8Clatest%E3%80%8D)のgolang環境で実行できます。

「--rm」オプションをつけていることによって、実行後にコンテナは破棄されます。ですが、まだイメージは残っているので、もう使用しないのであれば[イメージを削除する](https://zenn.dev/joho0724/articles/sankaku0724-newcreate15)ことも忘れないようにしましょう。

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

今回は、DockerでGo言語を導入してみました。Dockerを利用すれば、環境を手軽に試すことができます。この記事で紹介した方法以外にもGo言語を扱う方法は存在しているので、環境や用途に合わせて自身に合った方法を選択しましょう。

**皆さんも素敵なハッピーDockerライフを！！！🌸**
