---
title: "ZennのPV数をGoogle Analyticsで確認する"
emoji: "📈"
type: "tech"
topics:
  - "zenn"
  - "pv"
  - "googleanalytics"
  - "google"
  - "analytics"
published: false
---

## はじめに

今回は、ZennのPV数をGoogle Analyticsで確認する方法について紹介します。

## Google Analyticsって何？

[ヘルプ](https://support.google.com/analytics/answer/12159447?hl=ja)によると以下のように説明されています。

>Google アナリティクスは、ウェブサイトとアプリからデータを収集し、ビジネスに関する分析情報を提供するレポートを作成するプラットフォームです。

Google Analyticsを用いる事によって、Zennのトラフィックやユーザー行動を詳細に分析することができます。また、**無料で使用することができる**のもユーザーに優しいところですね。

## Google Analyticsの登録

Google Analyticsを利用するためには、登録設定を行う必要があります。
まず、[Google Analyticsの公式ページ](https://analytics.google.com)にアクセスしましょう。
アクセスしたら、**測定を開始**をクリックしてください。

![](/images/sankaku16/google1.png)

### アカウント作成

クリックすると、アカウント作成を求められます。
適当なアカウント名を入力しましょう。
また、アカウントのデータ共有設定を求められますが特に変更する必要はないので、進みましょう。

![](/images/sankaku16/google12.png)

### プロパティの作成

次は、プロパティの作成を求められます。
適当なプロパティ名を入力しましょう。（私は「Zenn-Analytics」と入力しました。）
また、レポートのタイムゾーンと通貨を日本のものにしておきましょう。

![](/images/sankaku16/google2.png)

### ビジネスの説明

次は、ビジネスの説明を求められます。
業種の部分は「オンラインコミュニティ」を選択しておけば問題ないと思われます。規模に関しては、一つのZennアカウントでのみ運用するため、小規模を選んでおいて大丈夫でしょう。

![](/images/sankaku16/google3.png)

### ビジネスの目標

次は、ビジネスの目標を求められます。
該当しそうなものを選択しましょう。

![](/images/sankaku16/google4.png)
*今になって考えたら「ユーザ行動の調査」も選択したほうが良かったかも⋯？*

### 利用規約の同意

次は、利用規約の同意を求められます。
しっかり読んでから同意しましょう。

![](/images/sankaku16/google5.png)

### データ収集の設定

次は、データ収集の設定を求められます。
今回はZennのPV数を収集するため、プラットフォームは「ウェブ」を選択しましょう。

![](/images/sankaku16/google6.png)

### データストリームの設定

次は、データストリームの設定を求められます。
ここでは、WebサイトのURLに「zenn.dev」と入力しましょう。
また、ストリーム名は適当なものを入力しておきましょう。（私は「Zenn PV」と入力しました。）

![](/images/sankaku16/google13.png)

### Google Analyticsの登録完了

これでGoogle Analyticsの登録は完了です。
後で用いる事になるので**測定IDは必ず控えておきましょう**。

![](/images/sankaku16/google7.png)

## ZennをGoogle Analyticsと連携させる

ZennをGoogle Analyticsと連携させていきます。
ヘッダー上の自身のアイコンをクリックして「アカウント設定」に進みましょう。

![](/images/sankaku16/google14.png)

そして、Google Analyticsの「トラッキングIDを設定」をクリックし、先ほど控えておいた測定IDを入力しましょう。
これでZennのPV数をGoogle Analyticsで確認することができるようになりました！

![](/images/sankaku16/google8.png)

## ZennのPV数を確認してみた

試しに[Google Analyticsの公式ページ](https://analytics.google.com)にアクセスしてみると、データ収集が有効であると表示されました。

![](/images/sankaku16/google9.png)

確認できるようになったことにより、日本各地で私の記事を閲覧してくれている人がいることが実感できました。とても嬉しいです！

![](/images/sankaku16/google10.png)

また、国別でもユーザー数を確認する事ができ、大韓民国🇰🇷やタイ🇹🇭でも私の記事が閲覧されていることが判明し、とても驚きました。~~ほんまか？~~

![](/images/sankaku16/google11.jpg)

## さいごに

ここまで記事を読んでくださり、ありがとうございました！今回は、ZennのPV数をGoogle Analyticsで確認する方法について紹介しました。興味を持った方は、ぜひGoogle Analyticsを利用してZennの執筆のモチベーションを上げていきましょう！

**皆さんも素敵なハッピーZennライフを！！！🌸**
