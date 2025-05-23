---
title: "IPv6基礎検定を受けてみた話"
emoji: "🦪"
type: "idea"
topics:
  - "ipv6"
  - "インフラ" 
  - "ポエム"
  - "network"
  - "合格体験記"
published: true
published_at: 2025-05-24 12:00
---

## はじめに

今回は「**IPv6基礎検定を受けてみた話**」ということで、記事を書いてみました。

拙い文章ではありますが、少しでも参考になれば幸いです。

## 対象読者

- **ネットワークが好きな人**
- IPv6に興味がある人
- IPv6基礎検定を受けるか悩んでいる人
- これからIPv6基礎検定を受ける人
- 暇つぶしに記事でも読んでみるか⋯って思ってる人

### 試験前の筆者の状況

- **IPv6、ほんとに全然知らない**
- IPv4の長さが32ビットで、IPv6アドレスの長さは128ビットなんでしょ！
**てか、それしか知らんわ！他に何が違うの？**

## IPv6基礎検定って？

[**IPv6基礎検定**](https://network-engineer.jp/ipv6basic)は、[一般社団法人日本ネットワーク技術者協会](https://network-engineer.jp/)様が2023年3月から開始した試験です。比較的新しめの試験ですね。

![](/images/sankaku45/IPv6basic.png)
*ロゴマーク*

公式ホームページによると、以下のように説明されています。

> 試験名称：IPv6基礎検定（英名：IPv6 Engineer Qualifying Basic Examination）
> 対象：初級ネットワークエンジニアとネットワークの運用管理を行う方
> ※初級ネットワークエンジニアとは：インフラエンジニアとしてネットワークの基礎知識がある方
> 概要：IPv6の基礎的な知識を問う試験
> 主教材での想定学習時間：約40時間を想定
> 設問数：40問
> 受験時間：60分
> 合格基準：70％正解
> 受験期間：通年
> 受験料金： 7,000円（税抜き）
> 試験会場：全国350か所のCBT-Solutionsテストセンター

この資格を取得していると、**IPv6に関する基礎的な知識を所持しているという証明**になります。

試験の申し込みは、以下の専用サイトから行うことができます。

https://cbt-s.com/examinee/examination/IPv6_basic.html

## なぜIPv6基礎検定を受けることにしたのか

[広島地域IPv6推進委員会](https://www.ipv6hiroshima.jp/)様が主催する[2024年度 IPv6勉強会](https://www.ipv6hiroshima.jp/%E3%82%BB%E3%83%9F%E3%83%8A%E3%83%BC%E3%82%A4%E3%83%99%E3%83%B3%E3%83%88/2024%E5%B9%B4%E5%BA%A6ipv6%E5%8B%89%E5%BC%B7%E4%BC%9Aipv6%E5%9F%BA%E7%A4%8E%E6%A4%9C%E5%AE%9A%E5%8F%97%E9%A8%93%E6%94%AF%E6%8F%B4)に参加したことがきっかけです。

「ネットワークを研究する人間として知識をつけたい⋯」と思っていた頃、[IPv6基礎検定に合格することでIPv6への理解度を図る](https://network-engineer.jp/archives/1345)ことを最終目標としているこの勉強会の存在を知り、「渡りに船だ！！」ということで参加しました。
学外の勉強会といったものに参加するのは初めてで不安もありましたが、温かく迎えていただいたおかげでリラックスして学びに集中することができました。
また、勉強会に参加することで他大学の研究者や社会人の技術者の方々と共に学ぶ機会を得ることができたり、IPv6勉強会に必要な書籍（[小川晃通 著, プロフェッショナルIPv6 第2版, ラムダノート](https://www.amazon.co.jp/dp/4908686114)）とIPv6基礎検定の受験に必要な費用を支給していただいたりするという充実した環境に身を置くことができたのはとても良い経験になりました。

https://x.com/triangle0724/status/1839629625854730243

**この場を借りて、関係各所の皆様に深く感謝申し上げます。**

## 学習期間と学習方法

想定学習時間が約40時間とのことでしたが、私も大体同じほどの期間で学習を行いました。

**学習方法としては、「[小川晃通 著, プロフェッショナルIPv6 第2版, ラムダノート](https://www.amazon.co.jp/dp/4908686114)」を主教材としました。**
試験の出題範囲はこの書籍から選定されています。

| ♯ | 出題内容 | 出題数 |
|---|----------|--------|
| 1：IPv6概論 | 第2章 IPv6概論<br>第3章 IPv6アドレス体系<br>第4章 IPv6パケットの構成 | 16問 |
| 2：ICMPv6、Neighbor Discovery、IPv6アドレスの自動設定 |　第5章 ICMPv6<br>第6章 近隣探索プロトコル<br>第7章 IPv6アドレスの自動設定<br>第8章 DHCPv6 | 12問 |
| 3：フラグメンテーション、Path MTU Discovery、マルチキャスト | 第9章 IPフラグメンテーション<br>第10章 Path MTU discovery<br>第11章 IPv6マルチキャスト | 4問 |
| 4：その他 | 第13章 IPv6におけるマルチアドレスと マルチプレフィックス<br>第14章 IPv6とセキュリティ<br>第16章 DNSの基礎とIPv6対応<br>第17章 DNSによるデュアルスタック環境の実現と運用<br>第21章 IPv4/IPv6共存技術の分類<br>第22章 トンネル技術<br>第23章 IPv4/IPv6変換技術<br>第24章 IPv4/IPv6共存技術の運用形態<br>第25章 プロキシ方式 | 8問 |
|   |  | 計40問 |

私もその書籍の重要な箇所を紙に書いて覚えたり、勉強会のために内容をまとめてプレゼン発表したりするといったようにして勉強しました。

また、**書籍の著者である小川晃通氏のIPv6解説動画も参考**にさせていただきました。

https://www.youtube.com/@geekpage

重要な部分をピンポイントで押さえることができたため、とても効果的でした。

また、**生成AIに疑似問題を作らせ、それを解く**といったこともしました。必ずしも生成AIが正しい知識をもとに問題を作成しているとは限らないため、そもそもの内容に破綻がないかをチェックする作業を挟むことで理解をさらに深めることができました。

そして、問題に取り組むだけでなく、研究室の先輩方にWiresharkでキャプチャしたIPv6パケットを実際に確認しながら解説していただいたり、疑似問題を出してもらったりするといったこともしました。
**周りの方に助けられた勉強期間でした🙇**

## 試験結果と感想

2025年4月27日に受験し、**合格**することができました！🥳

https://x.com/triangle0724/status/1916334786895118360

全体の問題の7割を正解することが合格基準のため、受験前は不安でいっぱいでしたが、実際に受けた感想としては**易しめの難易度**だと思いました。

もちろんノー勉で合格できるほど甘くはないでしょう。
しかし、教科書の重要語句やその意味など、IPv6に関する基本的な基礎知識や要点さえ抑えておけば大丈夫だと思います。

## さいごに

ここまで記事を読んでくださり、ありがとうございました！
私はIPv6に直接関係する研究をしているわけではないのですが、試験合格に向けて勉強することで、非常に充実した時間を過ごせたと思います。

ちなみに合格するとCBT-Solutionsから合格証明書のPDFが発行されます！

![](/images/sankaku45/IPv6_Result.jpg)
*ありがとうございます*

また、[日本ネットワーク技術者協会様に合格体験記を応募すると、特別なグッズを貰えます！](https://network-engineer.jp/apply)
私はIPv6マグカップにしました！

https://x.com/triangle0724/status/1924436660370252204

IPv6は今後も注目が集まる技術です。
興味を持った方はぜひIPv6基礎検定を受験してみてください！

:::details ここでIPv6コソコソ噂話！

朗報です！なんと、**試験の主教材の電子版は無料で利用できます！**

これからIPv6について勉強を始めようとしている方々にとって、非常に取り組みやすい環境が整います！
この機会を活用して、学習をスタートしてみてはいかがでしょうか？

https://booth.pm/ja/items/913273

:::

**皆さんも素敵なハッピーIPv6ライフを！！！🌸**
