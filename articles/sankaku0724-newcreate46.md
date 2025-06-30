---
title: "【Ubuntu】ns-3を使えるようにするまでの手順"
emoji: "🖥️"
type: "tech"
topics:
  - "ubuntu"
  - "network"
  - "データ分析"
  - "ubuntu2404"
  - "環境構築"
published: false
---

## はじめに

この記事は、Ubuntuでns-3を使えるようにするまでの手順を大まかにざっくりとまとめたものになります。

:::message
本記事で紹介するインストール方法は、**あくまでも一例**として提供しています。なお、これらの手順を実行したことによる結果や影響について、筆者は一切の責任を負いかねますので、ご了承ください。また、macOSでns-3を使えるようにするまでの手順は[こちらの記事](https://zenn.dev/joho0724/articles/sankaku0724-newcreate28)に記載してあります。
:::

### 私の動作環境
- Ubuntu 24.04.2 LTS (64-bit)

### ns-3のバージョン
- [3.35](https://www.nsnam.org/releases/ns-3-35/)

## ns-3ってなんやねん

**ns-3**は**オープンソースの離散事象ネットワークシミュレータ**であり、GNU GPLv2ライセンスの下で**無償で利用可能**です。柔軟性が高く、ユーザーが独自のモジュールを追加することで、さまざまなネットワーク技術や新しいプロトコルのシミュレーションを行うことができます。これにより、実機での実験が困難な大規模ネットワークや、まだ実装されていない新技術の理論的検証を行うことができます。

公式のホームページを確認したい人は、以下のリンクから飛ぶことができます。

https://www.nsnam.org/

## ns-3を導入する

:::message
コマンドの詳細や適用については、ご自身の環境に合わせて十分に調査し、ご判断ください。
:::

### 必要なものをインストールする

まずは、以下のコマンドでシステムを最新の状態に更新します。

```
sudo apt update && sudo apt upgrade -y
```

次に、以下のコマンドを実行し、シミュレーションを行うにあたって必要なものをインストールします。

```
sudo apt install -y build-essential python3 python3-dev python3-pip libsqlite3-dev libgsl-dev libboost-all-dev libxml2-dev gdb valgrind tcpdump
```

ここで、`sudo apt install python3`を実行すると、最新のPython3系バージョンがインストールされます。

![](/images/sankaku46/python12.png)
*インストールされたPython3のバージョン*

しかし、今回はns-3.35との動作実績や互換性の観点から3.9系のPythonを使用します。
そのため、以下の記事を参考にさせていただき、[deadsnakes](https://github.com/deadsnakes)というPPA（個人パッケージアーカイブ）から3.9系のPythonを別途インストールしました。

https://tokibito.hatenablog.com/entry/2021/09/04/164628

```:deadsnakesから3.9系のPythonをインストールするコマンド
sudo apt install -y software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install -y python3.9 python3.9-dev python3.9-distutils libpython3.9-dev
```

これで3.9系のPythonをインストールすることができました。

![](/images/sankaku46/python9.png)
*インストールされたPython3.9のバージョン*

### ns-3を使用できるようにする

必要なものをインストールしたら、作業を行いたい場所に移動しましょう。

```
cd ~
```

移動したら、以下のコマンドで公式のホームページからns-3をダウンロードします。

```
wget https://www.nsnam.org/releases/ns-allinone-3.35.tar.bz2
```

ダウンロードしたら、ファイルを展開します。
```
tar xjf ns-allinone-3.35.tar.bz2
```

展開したら、ns-3.35のディレクトリまで移動します。

```
cd ns-allinone-3.35/ns-3.35
```

移動したら、現在、`ns-allinone-3.35/ns-3.35/`ディレクトリにいるはずです。

ここで、C++ソースコードに修正を加えます。ns-3のコード内部では`uint32_t`のような固定幅整数型が頻繁に使われており、これらは`#include <cstdint>`というヘッダーファイルで定義されています。そのため、以下の3つのC++ソースコードにこのヘッダーファイルを追加記述します。

- `src/network/utils/bit-serializer.h`
- `src/network/utils/bit-deserializer.h`
- `src/wifi/model/block-ack-type.h`

#### 1. `bit-serializer.h`の修正

好きなエディタでソースコードを編集しましょう。

```:nanoで編集する場合
nano src/network/utils/bit-serializer.h
```

`#include <vector>`の下に`#include <cstdint>`を追加します。

```cpp
#include <vector>
#include <cstdint> // ← この行を追加
```

#### 2. `bit-deserializer.h`の修正

```:nanoで編集する場合
nano src/network/utils/bit-deserializer.h
```

`#include <deque>`の下に`#include <cstdint>`を追加します。

```cpp
#include <deque>
#include <cstdint> // ← この行を追加
```

#### 3. `block-ack-type.h`の修正

```:nanoで編集する場合
nano src/wifi/model/block-ack-type.h
```

`#include <vector>`の下に`#include <cstdint>`を追加します。

```cpp
#include <vector>
#include <cstdint> // ← この行を追加
```

修正したら、Python 3.9を指定して、`configure`（ビルド設定）を実行します。

```
PYTHON=python3.9 ./waf configure
```

成功したら、以下のコマンドでwafスクリプトを実行します。wafスクリプトを実行することで、ns-3のソースコードをコンパイルし、実行可能なシミュレータを生成することができます。

```
./waf build
```

成功したら、ns-3に初めから付属しているサンプルを実行してみます。

```
./waf --run scratch-simulator
```

:::details サンプルファイルの中身

```cpp:scratch-simulator.cc（実行すると「Scratch Simulator」と表示される）
#include "ns3/core-module.h"

using namespace ns3;

NS_LOG_COMPONENT_DEFINE ("ScratchSimulator");

int 
main (int argc, char *argv[])
{
  NS_LOG_UNCOND ("Scratch Simulator");

  Simulator::Run ();
  Simulator::Destroy ();
}
```

「Hello World」的なサンプルということですね。

:::

![](/images/sankaku46/hello.png)
*正常に実行できている*

これでUbuntuでns-3を使えるようになりました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！
今回は、Ubuntuでns-3を利用できるようにする方法について紹介しました。
ns-3を利用することで、さまざまなネットワーク環境のシミュレーションを効果的に行うことができます。
私もまだまだ不慣れなので、地道に触りながら学んでいきたいです。

**皆さんも素敵なハッピーns-3ライフを！！！🌸**
