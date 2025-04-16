---
title: "macOSでns-3を使えるようにするまでの手順"
emoji: "🛜"
type: "tech"
topics:
  - "mac"
  - "network"
  - "データ分析"
  - "test"
  - "環境構築"
published: true
published_at: 2025-04-19 12:00
---

## はじめに

この記事は、macOSでns-3を使えるようにするまでの手順を大まかにざっくりとまとめたものになります。
**コマンドの詳細や適用については、ご自身の環境に合わせて十分に調査し、ご判断ください**。

:::message
本記事で紹介するインストール方法は、**あくまでも一例**として提供しています。なお、これらの手順を実行したことによる結果や影響について、筆者は一切の責任を負いかねますので、ご了承ください。
:::

### 私の動作環境
- MacBook Air M2
- Apple clang version 16.0.0 (clang-1600.0.26.6)

### ns-3のバージョン
- [3.35](https://www.nsnam.org/releases/ns-3-35/)

## ns-3ってなんやねん

**ns-3**は**オープンソースの離散事象ネットワークシミュレータ**であり、GNU GPLv2ライセンスの下で**無償で利用可能**です。柔軟性が高く、ユーザーが独自のモジュールを追加することで、さまざまなネットワーク技術や新しいプロトコルのシミュレーションを行うことができます。これにより、実機での実験が困難な大規模ネットワークや、まだ実装されていない新技術の理論的検証を行うことができます。

公式のホームページを確認したい人は、以下のリンクから飛ぶことができます。

https://www.nsnam.org/

## ns-3を導入する

この記事では、macOSでXcodeやHomebrewを使える前提で話を進めていきます。
インストールしていない場合は、以下のコマンドを実行しましょう。

```
xcode-select --install
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew update
```

ここからが本題です。事前準備として以下のコマンドを実行し、シミュレーションを行うにあたって必要なものをインストールします。

```
brew install qt mercurial libxml2 pyenv gsl libgcrypt gtk cvs unar p7zip xz bzt bar dzr && brew install --cask doxygen cmake && pip3 install meson
```

:::message
場合によっては、割と時間がかかるかもしれません。
私の場合は一時間以上かかりました⋯
:::

次にpyenvを使って3.9系のPythonをインストールします。

```
pyenv install 3.9.13
pyenv global 3.9.13
```

そして、ターミナルを開いたときに自動で設定が有効になるように、以下のようなコマンドを実行します。

```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zprofile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zprofile
echo 'eval "$(pyenv init --path)"' >> ~/.zprofile
source ~/.zprofile
```

ここまで無事に成功したら、公式のホームページからns-3をダウンロードします。
以下のリンクから、バージョン3.35をダウンロードすることができます。

https://www.nsnam.org/releases/ns-allinone-3.35.tar.bz2

ダウンロードしたら、解凍してns-3.35のディレクトリまで移動します。

```
cd ns-allinone-3.35/ns-3.35
```

移動したら、以下のコマンドでwafスクリプトを実行します。wafスクリプトを実行することで、ns-3のソースコードをコンパイルし、実行可能なシミュレータを生成することができます。

```
CXXFLAGS="-std=c++17" ./waf configure
./waf build
```

`CXXFLAGS`はC++のコンパイル時のフラグを渡すために使う変数であり、`-std=c++17`は、「**このプロジェクトをC++17でビルドしてね**」という指定をするものです。
ns-3.35は一部の機能が`C++17`を前提としており、macOSに標準で入っているApple clangを使っている場合、デフォルトのC++規格が`c++17`未満になっていることが多く、明示的に`-std=c++17`を指定しないとns-3.35のビルドが失敗することがあります。私はうまくいきませんでした。
~~普通にGCC使えば良くないとか言われたらぐうの音も出ません~~

成功したらサンプルを実行してみます。

```
./waf --run scratch-simulator
```

![](/images/sankaku28/1.jpg)
*正常に実行できている*

これでmacOSでns-3.35を使えるようになりました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

今回は、ns-3を利用できるようにするための環境構築方法について紹介しました。

ns-3を利用することで、さまざまなネットワーク環境のシミュレーションを効果的に行うことができます。

私もまだまだ不慣れなので、少しずつ学んでいきたいです。

**皆さんも素敵なハッピーns-3ライフを！！！🌸**
