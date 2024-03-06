---
title: "MacのカーソルをWiiにしてみた"
emoji: "🎮"
type: "tech"
topics:
  - "mac"
  - "カーソル"
  - "macbook"
  - "m1mac"
  - "macbookair"
published: false
---

## はじめに
今回は、MacBook Airのマウスカーソルの見た目をWiiリモコンのポインターにする方法について紹介します。

![](/images/sankaku5/wii1.png)

少しでも参考になれば幸いです。

## 経緯
なぜ私がMacのカーソルをWii仕様に変更しようとしたかというと、𝕏（旧Twitter）でWindowsのカーソルをWiiリモコンのポインターにしている投稿が流れてきたからです。

https://twitter.com/anal_ikiikisuto/status/1733815289383624865

すごい！自分もこれやってみたい！と思った単純な私は、即座に真似をしようと決意しました。

## 手順

### 画像の取得

まず、カーソルにしたい画像を用意する必要がありました。私は[deviantART](https://www.deviantart.com/)というdeviantART社が運営するインターネットコミュニティから、Wiiリモコンのポインターの画像を取得しました。初めて使用する人は、アカウントを作成することでdeviantARTを利用することができます。ちなみに基本無料です。

以下のリンクから、Wiiリモコンのポインターの画像を取得して利用しました。

https://www.deviantart.com/marcoszone/art/Handwii-44077926

ここから、以下に示されているような６つのcur画像（カーソル変更に使用するための画像）を取得することができます。
![](/images/sankaku5/preview2.png)

![](/images/sankaku5/preview.jpg)

### カーソル変更

先ほどの𝕏の投稿者の方は[Windowsのカーソルを変更する方法について紹介していました](https://twitter.com/anal_ikiikisuto/status/1733830574119469193)が、私が所持しているのはMacBook Airでした。また、Windowsでは設定からカーソルを簡単に変更できるらしいのですが、Macではそのようなことができません。

そこで、macOS向けのカスタムマウスカーソルの作成と管理ができる**Mousecape**というツールを使用しました。これもまた、無料で使用することができます。

Mousecapeは以下のリンクから入手することができます。**Mousecape_1813.zipをダウンロードして展開**しましょう。展開した後は、アプリケーション一覧に入れておくと良いでしょう。

https://github.com/alexzielenski/Mousecape/releases

ここで、アプリを開こうとした際に、悪質なソフトウェアであるかどうかAppleで確認できない旨のメッセージが表示され、開くことができない場合があります。

![](/images/sankaku5/mouse1.png)

この問題を解決する方法を調べると、[macOSユーザガイド](https://support.apple.com/ja-jp/guide/mac-help/mchleab3a043/mac)に解決方法が記載されていました。
**Controlキーを押しながらアプリアイコンをクリックして、ショートカットメニューから「開く」を選択してクリックする**ことで、セキュリティ設定の例外として保存され、いつでもダブルクリックすることで開くことができるようになるとのことです。私はその手順を実行して解決しました。

そして、以下のサイトを参考にさせていただき、カーソルを設定しました。

https://note.com/rocky_mountains/n/n5a300c96badd

https://note.com/yaenbibi/n/nb762d12f2ccb#a4d7cf7f-f227-4e1b-92c0-cfe6f91dffb0

一応、大雑把な手順の解説をします。

上のFileからNew Capeを選択し、作成された物をクリックしてEditを選択しましょう。
![](/images/sankaku5/mouse2.png)

![](/images/sankaku5/mouse3.png)

自身がわかりやすいようにNameとAuthorを変更しましょう。私はここでtestとsankakuと名付けてあります。
![](/images/sankaku5/mouse4.png)

ここで、一番下にあるRetinaについて調べてみましたが、あまりよくわかりませんでした。そこで、Chat GPTに質問してみると、

> 「Mousecape」は、macOS用のカーソルテーマを管理するためのユーティリティアプリケーションです。そして「retina」という言葉は、高解像度のディスプレイを指すことが一般的です。
> 一般的に、"Retina" ディスプレイは、通常のディスプレイよりも高いピクセル密度を持ち、より鮮明な画像を提供します。そのため、"Mousecape"の"retina"という用語は、高解像度ディスプレイ向けのカーソルテーマである可能性があります。つまり、解像度が高いディスプレイでカーソルを鮮明に表示するための特別なテーマを指すかもしれません。

と言われました。チェックを入れておいておそらく問題ない（今のところ、私はチェックを入れて困っていないので）と思われます。

そして、左下の「＋」を押すことでカーソルを追加することができるようになります。

![](/images/sankaku5/wii2.png)

1xの部分に取得した画像をそれぞれドラッグ&ドロップして設定しました。

ちなみに私は以下の六つの項目の設定をしました。この項目は先ほど紹介したサイトを参考にさせていただき、設定しています。
1. Arrow
wii_normal.curを適応。
2. Busy
wii_busy2.curを適応。
3. Closed
wii_url_mini.curを適応。
4. Forbidden
wii_invalid.curを適応。
5. Help
wii_help.curを適応。
6. Pointing
wii_url_big.curを適応。

設定が終了した際は保存し、ダブルクリックをするか`⌘(command)+⏎`を入力することで、設定したカーソルの状態(今回はWii仕様)に変更されるはずです。

## 結果

MacBook Airのマウスカーソルの見た目をWiiリモコンのポインターに変更することができました！

![](/images/sankaku5/wiiresult.png)

カーソルを変更することで、PCなのに**Wiiで遊んでいるような錯覚に陥ることができます！！！**

また、Mousecape上で`⌘(command)+R`を入力するとデフォルトに戻すことができるのでお手軽です！

私はカーソルをWiiにした状態で、任天堂のWii公式サイトにアクセスし、Wiiの気分を味わって楽しんでいます。

https://www.nintendo.co.jp/wii/rhaj/mii/index.html

## さいごに
ここまで記事を読んでくださり、ありがとうございました！

今回、私はWii仕様に変更しましたが、別の画像を適応させることもできます！推しの画像に変更するといったこともでき、自由自在です！モチベーションが少しでも上がれば、作業効率も上がるかもしれません！

面白そうだなと思った方は、カーソルをお気に入りの画像に変えてみてはいかがでしょうか？

**皆さんも素敵なハッピーカーソルライフを！！！🌸**
