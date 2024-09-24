---
title: "Xcodeをインストールする"
emoji: "🔨"
type: "tech"
topics:
  - "xcode"
  - "mac"
  - "apple"
  - "appstore"
  - "m1"
published: false
---

## はじめに

この記事は、App StoreからXcodeをmacOSにインストールする方法をまとめたものになります。

### 私の動作環境
- MacBook Air M1 2020
- メモリ 16GB
- MacOS Sonoma 14.6.1

## Xcodeってなんやねん

Xcodeは、Appleが提供している**統合開発環境**(IDE)で、主にmacOSやiOSなどのアプリケーションの開発に使用されます。

公式ドキュメントを確認したい人は、以下のリンクから飛ぶことができます。

https://developer.apple.com/documentation/xcode

また、日本語版を参照したい方は、以下のリンクから飛ぶことができます。

https://developer.apple.com/jp/xcode/

## App StoreからXcodeをインストールする

まず、App Storeを開きます。
開いたら、検索バーに「Xcode」と入力し、検索します。
検索後はXcodeのページに移動し、「入手」をクリックします。

![](/images/sankaku32/3.png)
*~~評価あんまり良くないな⋯~~開発ツールランキング1位じゃん！！*

完了するまで待ちましょう。私の環境では大体10分程度かかりました。

完了したら、Xcodeの利用に関する「[**Xcode and Apple SDKs Agreement**](https://www.apple.com/legal/sla/docs/xcode.pdf)」のライセンス契約内容を確認し、同意しましょう。
これにより、Xcodeの利用を進めることができます。

![](/images/sankaku32/4.png)
*「Agree（同意する）」をクリック*

同意すると、以下のような画面が表示されます。「Install」をクリックしましょう。

![](/images/sankaku32/5.png)

クリックすると、インストールが始まります。

![](/images/sankaku32/6.png)

インストールが完了すると、以下のような画面が表示されます。
この画面は、Xcodeがフレームワークの更新を適用するために再起動が必要であることを示しています。
再起動後、最新のフレームワークや更新が反映され、引き続きXcodeを使用できるようになります。

![](/images/sankaku32/7.png)
*「Relaunch Xcode」をクリック*

再起動すると、以下のような画面が表示されます。次に進みましょう。

![](/images/sankaku32/8.png)
*「Continue」をクリック*

進むと、以下のような画面が表示されます。これでセットアップは完了です。

![](/images/sankaku32/9.png)

Xcodeを使えるようになりました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

私も初心者なので、少しずつ触っていくことで慣れていきたいです。

**皆さんも素敵なハッピーXcodeライフを！！！🌸**
