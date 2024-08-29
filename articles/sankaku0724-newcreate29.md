---
title: "ns-3をmacOSにインストールする"
emoji: "🛜"
type: "tech"
topics:
  - "mac"
  - "network"
  - "ネットワーク"
  - "シミュレーション"
  - "sonoma"
published: false
---

## はじめに

今回は，私がns-3をmacOSにインストールした際にしたことをまとめてみました．

:::message
本記事で紹介するインストール方法は、あくまで一例として提供しています。コマンドの詳細や適用については、ご自身の環境に合わせて十分に調査し、ご判断ください。なお、これらの手順を実行したことによる結果や影響について、筆者は一切の責任を負いかねますので、ご了承ください。
:::

### 私の動作環境
- MacBook Air M1 2020
- MacOS Sonoma 14.6

### ns-3のバージョン
- [3.35](https://www.nsnam.org/releases/ns-3-35/)

## ns-3ってなんやねん

**ns-3**は**オープンソースの離散事象ネットワークシミュレータ**であり，GNU GPLv2ライセンスの下で**無償で利用可能**です．柔軟性が高く、ユーザーが独自のモジュールを追加することで、さまざまなネットワーク技術や新しいプロトコルのシミュレーションを行うことができます。これにより、実機での実験が困難な大規模ネットワークや、まだ実装されていない新技術の理論的検証を行うことができます。

公式のホームページを確認したい人は、以下のリンクから飛ぶことができます。

https://www.nsnam.org/

## ns-3を導入する

この記事では，macOSでXcodeコマンドラインツールやHomebrewを使える前提で話を進めていきます．
インストールしていない場合は、以下のコマンドを実行しましょう．

```
xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew update
```

ここからが本題です．事前準備として，以下のコマンドを実行し、シミュレーションを行うにあたって必要なものをインストールします．

```
brew install qt mercurial libxml2 pyenv gsl libgcrypt gtk cvs unar p7zip xz bzt bar dzr && brew install --cask doxygen cmake && pip3 install meson
```

:::message
場合によっては，割と時間がかかるかもしれません．
私の場合は一時間以上かかりました⋯
:::

インストールが成功したら，公式のホームページからns-3をダウンロードします．
私は以下のリンクから，バージョン3.35をダウンロードすることにしました．

https://www.nsnam.org/releases/ns-allinone-3.35.tar.bz2

ダウンロードしたら，解凍してns-3.35のディレクトリまで移動します．

```
cd ns-allinone-3.35/ns-3.35
```

移動したら，以下のコマンドでwafスクリプトを実行します．wafスクリプトを実行することで、ns-3のソースコードをコンパイルし、実行可能なシミュレータを生成することができます．

```
./waf clean
./waf configure -d debug --disable-werror --enable-examples --enable-tests
./waf
```

:::details 「operation not permitted: ./waf」が出てきて実行できない場合

:::message
実行できない場合は，以下のコマンドを実行します．

```
xattr -l waf
xattr -c waf
```

これらのコマンドは、macOSのファイル拡張属性を操作するために使用されます。

1. `xattr -l waf`

   このコマンドは、`waf`ファイルに関連付けられた拡張属性のリストを表示します。
   - `-l`オプションは「list」を意味し、全ての拡張属性を表示します。

2. `xattr -c waf`

   このコマンドは、`waf`ファイルから全ての拡張属性を削除します。
   - `-c`オプションは「clear」を意味し、全ての拡張属性を消去します。

macOSは、ダウンロードしたファイルに[**`com.apple.quarantine`という拡張属性**](https://iboysoft.com/jp/news/com-apple-quarantine.html)を付与することがあります。この属性は、ファイルが信頼できないソースからダウンロードされたことを示し、実行を制限する可能性があります。これらのコマンドを使用することで、wafスクリプトの実行に関する問題を解決できるかもしれません。

![](/images/sankaku28/1.png)
*私はこの作業を行うと実行できるようになりました*

:::




```
./waf --run hello-simulator
```

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

ns-3を利用することで，

興味を持った方は、ぜひ一度ns-3を使ってみてください！私も

**皆さんも素敵なハッピーns-3ライフを！！！🌸**
