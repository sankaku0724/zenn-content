---
title: "【macOS】実行権限もあるし、絶対に正しいsudoパスワードも打ち込んでるのに実行できない時はこうするんや！"
emoji: "👊"
type: "tech"
topics:
  - "mac"
  - "sudo"
  - ""
  - ""
  - "sonoma"
published: false
---

## はじめに

絶対に正しいパスワードを入力しているのに弾かれてしまう⋯実行権限もちゃんとある⋯


### 私の動作環境
- MacBook Air M1 2020
- MacOS Sonoma 14.6

## 結論

## 説明

macOSは、ダウンロードしたファイルに[**`com.apple.quarantine`という拡張属性**](https://iboysoft.com/jp/news/com-apple-quarantine.html)を付与することがあります。この属性は、ファイルが信頼できないソースからダウンロードされたことを示し、実行を制限する可能性があります。

![](/images/sankaku29/1.png)

![](/images/sankaku29/2.png)

![](/images/sankaku29/3.png)

![](/images/sankaku29/4.png)

![](/images/sankaku29/5.png)


## さいごに

ここまで記事を読んでくださり、ありがとうございました！



興味を持った方は、ぜひ一度ns-3を使ってみてください！私も

**皆さんも素敵なハッピーsudoライフを！！！🌸**
