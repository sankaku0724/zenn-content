---
title: "【macOS】実行権限もあるし、絶対に正しいsudoパスワードも打ち込んでるのに実行できない時はこうすればいいんや！"
emoji: "👊"
type: "tech"
topics:
  - "mac"
  - "sudo"
  - "security"
  - "m1"
  - "sonoma"
published: false
---

## はじめに

実行権限はちゃんとある⋯

![](/images/sankaku29/2.png)

絶対に正しいsudoパスワードを入力しているのに弾かれてしまう⋯

![](/images/sankaku29/3.png)
*lsコマンドをsudoで実行して，実行したいファイルの存在とsudoパスワードが正しいことを確認*

![](/images/sankaku29/1.png)
*同じパスワード入力しただろ！と゛お゛し゛て゛た゛よ゛お゛お゛お゛！*

**このファイル…何か変…**

~~PCに詳しい知り合いの建築技師の方に聞いてみようと思います．~~
ということで，この問題をどうやったら解消できるのか調べてみました．

### 私の動作環境
- MacBook Air M1 2020
- MacOS Sonoma 14.6

## 結論

```:魔法のコマンド
xattr -c <ファイル名>
```

これを実行すれば，問題は解消します．

## 説明

実行権限を確認した際に「@」がついてました．

![](/images/sankaku29/6.png)

macOSは、ダウンロードしたファイルに[**`com.apple.quarantine`という拡張属性**](https://iboysoft.com/jp/news/com-apple-quarantine.html)を付与することがあります。この属性は、ファイルが信頼できないソースからダウンロードされたことを示し、実行を制限する可能性があります。




![](/images/sankaku29/4.png)

![](/images/sankaku29/5.png)


## さいごに

ここまで記事を読んでくださり、ありがとうございました！


セキュリティが高すぎるとこんなこともあるんですね．

**皆さんも素敵なハッピーsudoライフを！！！🌸**
