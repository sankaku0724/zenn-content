---
title: "【macOS】NetAnimでネットワークシミュレーションを可視化する"
emoji: "👀"
type: "tech"
topics:
  - "mac"
  - "network"
  - "cpp"
  - "プログラミング"
  - "環境構築"
published: true
published_at: 2025-05-10 12:00
---

## はじめに

この記事では、macOSにおけるNetAnimのインストールと起動方法、そして簡単な使い方について解説します。
**コマンドの詳細や適用については、ご自身の環境に合わせて十分に調査し、ご判断ください**。

:::message
本記事で紹介するインストール方法は、**あくまでも一例**として提供しています。なお、これらの手順を実行したことによる結果や影響について、筆者は一切の責任を負いかねますので、ご了承ください。
:::

### 動作環境
- MacBook Air M2
- Apple clang version 16.0.0 (clang-1600.0.26.6)
- ns-3.35がインストールされている（netanim-3.108を含む）
- Homebrewがインストールされている

:::message
macOSでns-3.35を使えるようにするまでの手順に関しては、私が以前投稿した[**【macOS】ns-3を使えるようにするまでの手順**](https://zenn.dev/joho0724/articles/sankaku0724-newcreate28)という記事を見ていただけると幸いです。
また、もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

## NetAnimってなんやねん

[**NetAnim**](https://www.nsnam.org/wiki/NetAnim)は、**ns-3シミュレーションのアニメーションを視覚的に確認できるGUIツール**です。
シミュレーション中に生成されたXMLファイルを用いて、以前に実行されたシミュレーションをアニメーション化することができます。

## NetAnimを導入する

まず、HomebrewでQt5をインストールします。
QtはC++でGUIアプリを作るためのフレームワークです。

```
brew install qt@5
```

次に、NetAnimディレクトリへ移動します。

```:私の場合
cd ~/ns-allinone-3.35/netanim-3.108
```

:::message
パスは、ns-3を展開した場所に応じて変更してください。
:::

移動したら、Qtのビルドツールであるqmakeを使って、NetAnimのビルド用Makefileを生成します。

```
/opt/homebrew/opt/qt@5/bin/qmake NetAnim.pro
```

Makefileを生成したら、NetAnimのソースコードをコンパイルして、実行ファイルを作成します。

```
make
```

これでNetAnimの導入は完了です！

## NetAnimを起動し、ネットワークシミュレーションを可視化する

では、NetAnimでネットワークシミュレーションを可視化してみます。
私は以下の記事を参考にさせていただき、ns-3内に初めから用意されている**UDPエコー通信を1回送るだけ**のサンプルコード「first.cc」を用いてシミュレーションしました。

https://qiita.com/dorapon2000/items/4b51c1776f65b3fc7d26

まず、ns-3内に用意されている「examples/tutorial/first.cc」をns3ルートフォルダ直下にあるscratchフォルダにコピーしましょう。私は、参考記事と同様に「myfirst.cc」という名前に変更して保存しました。

コピーしたら「myfirst.cc」を編集します。編集といってもそれほど大袈裟なものではありません。
NetAnimでシミュレーションを可視化するには`AnimationInterface`を使って**XMLファイルを出力する必要**があります。
そのため、以下のようにコードを数行追加するだけで対応できます。

```cpp
#include "ns3/netanim-module.h"  // ← 追加
```

```cpp
// 以下を Simulator::Run () の前に追加
AnimationInterface anim ("myfirst_animation.xml");  // ← XMLファイル出力
```

また、アニメーションを見やすくするために以下の記述も追加し、ノードの表示位置を変更します。

```cpp:（追加するかどうかは任意）
anim.SetConstantPosition (nodes.Get (0), 10.0, 20.0);  // 追加
anim.SetConstantPosition (nodes.Get (1), 30.0, 20.0);  // 追加
```

:::details コードを追加した「myfirst.cc」の全体像
```cpp:myfirst.cc
#include "ns3/core-module.h"
#include "ns3/network-module.h"
#include "ns3/internet-module.h"
#include "ns3/point-to-point-module.h"
#include "ns3/applications-module.h"

#include "ns3/netanim-module.h"  // 追加
 
using namespace ns3;

NS_LOG_COMPONENT_DEFINE ("FirstScriptExample");

int
main (int argc, char *argv[])
{
  CommandLine cmd (__FILE__);
  cmd.Parse (argc, argv);
  
  Time::SetResolution (Time::NS);
  LogComponentEnable ("UdpEchoClientApplication", LOG_LEVEL_INFO);
  LogComponentEnable ("UdpEchoServerApplication", LOG_LEVEL_INFO);

  NodeContainer nodes;
  nodes.Create (2);

  PointToPointHelper pointToPoint;
  pointToPoint.SetDeviceAttribute ("DataRate", StringValue ("5Mbps"));
  pointToPoint.SetChannelAttribute ("Delay", StringValue ("2ms"));

  NetDeviceContainer devices;
  devices = pointToPoint.Install (nodes);

  InternetStackHelper stack;
  stack.Install (nodes);

  Ipv4AddressHelper address;
  address.SetBase ("10.1.1.0", "255.255.255.0");

  Ipv4InterfaceContainer interfaces = address.Assign (devices);

  UdpEchoServerHelper echoServer (9);

  ApplicationContainer serverApps = echoServer.Install (nodes.Get (1));
  serverApps.Start (Seconds (1.0));
  serverApps.Stop (Seconds (10.0));

  UdpEchoClientHelper echoClient (interfaces.GetAddress (1), 9);
  echoClient.SetAttribute ("MaxPackets", UintegerValue (1));
  echoClient.SetAttribute ("Interval", TimeValue (Seconds (1.0)));
  echoClient.SetAttribute ("PacketSize", UintegerValue (1024));

  ApplicationContainer clientApps = echoClient.Install (nodes.Get (0));
  clientApps.Start (Seconds (2.0));
  clientApps.Stop (Seconds (10.0));

  AnimationInterface anim ("myfirst_animation.xml");  // 追加
  anim.SetConstantPosition (nodes.Get (0), 10.0, 20.0);  // 追加
  anim.SetConstantPosition (nodes.Get (1), 30.0, 20.0);  // 追加

  Simulator::Run ();
  Simulator::Destroy ();
  return 0;
}
```
:::

「myfirst.cc」を用意したら、実行します。実行すると「myfirst_animation.xml」が生成されます。

```
./waf --run scratch/myfirst
```

XMLファイルが用意できたら、以下のようにしてNetAnimを起動します。

```
./NetAnim
```

起動すると以下のような画面のGUIが起動します。

![](/images/sankaku44/1.jpg)

左上のオレンジ色のファイルアイコンを選択して、「myfirst_animation.xml」を読み込みましょう。読み込むと、以下のようなアニメーションを確認することができました。

![](/images/sankaku44/myfirst.gif)
*可視化に成功！*

これでNetAnimを起動し、UDPエコー通信を1回送るだけのシンプルなネットワークシミュレーションを可視化することができました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！
NetAnimを利用することで、ネットワーク環境のシミュレーションを視覚的に確認することができます。
今回はサンプルファイルを実行しましたが、自身で用意した任意のファイルを可視化することも可能です。
私もまだまだ不慣れなので、少しずつ学んでいきたいです。

**皆さんも素敵なハッピーNetAnimライフを！！！🌸**
