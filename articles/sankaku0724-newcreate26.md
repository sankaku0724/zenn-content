---
title: "Gnuplotで簡単に作る美しいグラフ"
emoji: "📊"
type: "tech"
topics:
  - "gnuplot"
  - "mac"
  - "入門"
  - "homebrew"
  - "cli"
published: false
---

## はじめに

皆さんはグラフや図を作成する時にどんなツールを使っていますか？

PowerPoint？Excel？もしくはそれ以外？

この記事を読んでいるあなたに、**Gnuplot**という選択肢をぜひ知ってほしいです。

### 私の動作環境
- MacBook Air M1 2020
- MacOS Sonoma 14.5

## Gnuplotってなんやねん

[Gnuplot](http://www.gnuplot.info/)は、**データや関数のグラフを描画するためのツール**です。コマンドラインベースで操作し、数式やデータを入力することで、様々な形式でグラフを出力することができます。特に科学技術計算やデータ解析の分野で広く利用されており、簡単な計算やデータの視覚化をする際に便利です。

## Gnuplotをインストールする

私はmacOS上で以下のコマンドを実行し、[Homebrew](https://formulae.brew.sh/formula/gnuplot)経由でGnuplotをインストールしました。

```
brew install gnuplot
```

## Gnuplotで図やグラフを表示させる

実際にGnuplotを使ってみます。

ターミナルを開き、gnuplotと入力して起動すると、プロンプトがgnuplot>に変わります。

```
gnuplot
```

![](/images/sankaku26/1.png)

ここで、以下のようにコマンドを打ち込み、sin(x)のグラフを表示させてみます。

```
set title "Example Graph"
set xlabel "X-axis"
set ylabel "Y-axis"
plot sin(x) title "Sine Wave"
```

:::details コマンドの内容と説明
- set title "Example Graph"
グラフのタイトルを`Example Graph`に設定します。タイトルはグラフの上部に表示されます。

- set xlabel "X-axis"
X軸のラベルを`X-axis`に設定します。このラベルはグラフのX軸（横軸）に表示されます。

- set ylabel "Y-axis"
Y軸のラベルを`Y-axis`に設定します。このラベルはグラフのY軸（縦軸）に表示されます。

- plot sin(x) title "Sine Wave"
このコマンドで、Gnuplotはsin(x)関数をプロットし、グラフの曲線に`Sine Wave`というタイトルをつけます。plotコマンドは、データや関数をグラフ上に描画するために使われます。
:::

![](/images/sankaku26/2.png)

実行すると、ウィンドウが表示され、sin(x)のグラフが確認できました！

![](/images/sankaku26/3.png)
*sin(x)のグラフ*

このように、Gnuplotは数式やデータを入力することでグラフを出力することができます。

## Gnuplotで図やグラフを作成して保存する

では、上記のグラフをpng画像として保存するために、以下のようにコマンドを打ち込みます。

```
set terminal png
set output 'sine_wave.png'
set title "Example Graph"
set xlabel "X-axis"
set ylabel "Y-axis"
plot sin(x) title "Sine Wave"
set output
```

:::details 図として保存する手順
- set terminal png
保存したいファイルの形式を指定します。
他の形式を選びたい場合は、`png`を`jpeg`,`pdf`,`svg`,`eps`などにも変更できます。

- set output 'sine_wave.png'
出力ファイル名を指定します。
これで、「sine_wave.png」という名前のファイルにグラフが保存されます。

- グラフを描画するコマンド
今回の場合は、先ほどのプロットコマンドをそのまま使用しています。

- set output
Gnuplotに描画が完了したことを通知します。
:::

![](/images/sankaku26/4.png)

このコマンドを実行すると、カレントディレクトリに「sine_wave.png」というファイル名で、以下のような図を保存することができました。

![](/images/sankaku26/sine_wave.png)
*sine_wave.png*

Gnuplotを使うと、簡単に図を保存することができます！

## gpファイルを利用してもっと簡単に！

さて、Gnuplotで様々な形式の図を作成して保存することができるようになりましたが、毎回一つ一つコマンドを入力するのは面倒です。打ち間違えも怖いですしね。

そこで、**gpファイル**を利用します。
gpファイルは、gnuplotで使用するスクリプトファイルの一種です。これらのファイルには、gnuplotで実行するコマンドをテキスト形式で記述することができます。gpファイルを使用することで、逐次的にコマンドを入力する代わりに、あらかじめ設定したコマンドを一度に実行することが可能になります。

では、「plot_cosine.gp」という名前のgpファイルを用意し、以下のように書き込んでみます。
また、今まではsin(x)関数をプロットしていましたが、今回はcos(x)関数をプロットしてみます。

```gp:plot_cosine.gp
# 保存したいファイルの形式をpngに設定（jpeg、pdfなどに変更可能）
set terminal png

# 出力ファイル名を設定
set output 'cosine_wave.png'

# グラフのタイトルとラベルを設定
set title "Example Graph"
set xlabel "X-axis"
set ylabel "Y-axis"

# コサイン波をプロット（先ほどはサイン波だった部分を変更）
plot cos(x) title "Cosine Wave"

# 出力を閉じてファイルを確定
set output
```

書き込んだら、以下のコマンドを実行して、「plot_cosine.gp」ファイルを実行します。

```
gnuplot plot_cosine.gp
```

![](/images/sankaku26/5.png)

実行すると、カレントディレクトリに「cosine_wave.png」というファイル名で、以下のような図を保存することができました。

![](/images/sankaku26/cosine_wave.png)
*cosine_wave.png*

gpファイルを使用することで、一回のコマンドで一連の処理を実行でき、より簡単に図を保存することができます！
また、gpファイルに書き込むことにより、再利用性や自動化、共有性などの多くのメリットを手に入れることもできます。

**Gnuplotを使うときはgpファイルを！！！！！**

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

Gnuplotは便利なツールです。今回作成したpng画像以外にも**様々な形式で図やグラフを作成することができます。**

![](/images/sankaku26/animation.gif)
*私がGnuplotで作成したサイン波が徐々に動くアニメーション*

論文やプレゼンに使う素材を生成する際に、大いに役立ってくれるでしょう！

**皆さんも素敵なハッピーGnuplotライフを！！！🌸**
