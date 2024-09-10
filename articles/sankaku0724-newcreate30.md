---
title: "Speaker Deckで手軽にスライドを共有"
emoji: "🖼️"
type: "idea"
topics:
  - "zenn"
  - "powerpoint"
  - "pdf"
  - "初心者"
  - "speakerdeck"
published: false
---

## はじめに

[ZennのMarkdown記法一覧](https://zenn.dev/zenn/articles/markdown-guide#speakerdeck)を見ると、以下のような記述があります。

```
@[speakerdeck](スライドのID)
```

Speaker Deckってなんや？ということで実際に試してみることにしました。

## Speaker Deckってなんやねん

**Speaker Deck**は、PDFファイルをアップロードすることで簡単に**スライドをWeb上で共有**できるサービスです。
ユーザーは作成したスライドをPDF形式でアップロードし、オンラインで閲覧可能にすることができます。

また、サイト内では検索が可能で、**他の人のスライドを閲覧する**ことができます。

Speaker Deckには以下のリンクからアクセスできます。

https://speakerdeck.com/

## Speaker Deckを使ってみた

ということで、私も実際にSpeaker Deckを使ってスライドを共有してみることにしました。

### Speaker Deckの登録

まず、Speaker Deckのリンクにアクセスし、アカウント登録をします。

![](/images/sankaku30/1.png)

`Sign up for free`をクリックすると、以下のような画面が表示されます。

![](/images/sankaku30/2.png)

好きな方法で登録しましょう。
ちなみに私はGithubで登録しました。

![](/images/sankaku30/3.png)
*アカウント登録完了*

また、アカウント設定から日本語にすることができます。やりたい方はやりましょう。

![](/images/sankaku30/4.png)
*日本語表示に変更*

### スライドを共有できるようにする

では、スライドを共有できるようにします。
「デッキをアップロードする」をクリックすると、以下のような画面が表示されます。

![](/images/sankaku30/5.png)

公開したいPDFファイルを選択し、題名やディスクリプション（この二つは入力しなくてもいいらしい）、公開範囲を設定しましょう。

![](/images/sankaku30/6.png)

これによって、スライドを共有することができました！

共有用のリンクはスライドの右下から取得することができます。

![](/images/sankaku30/7.png)
*`Copy like URL`をクリック*

以下のリンクから共有されたスライドを確認することができます！

https://speakerdeck.com/sankaku0724/sankakunotesuto

### Zennにスライドを埋め込んでみる

では、Speaker DeckにアップロードしたスライドをZennに埋め込んでみます。
ここで、スライドのIDの値を確認するために、Speaker Deck上で`Copy javaScript embed code`を選択し、内容をコピーします。

![](/images/sankaku30/8.png)

私のテストスライドの場合は以下のようにコピーされました。

```js:私のテストスライドの場合
<script defer class="speakerdeck-embed" data-id="01c5d829d9e0476b8fbfe634d0353a08" data-ratio="1.3333333333333333" src="//speakerdeck.com/assets/embed.js"></script>
```

ここからスライドのIDである`data-id`の値を抜き出し、[ZennのMarkdown記法一覧](https://zenn.dev/zenn/articles/markdown-guide#speakerdeck)に則って記述してみます。

```:私のテストスライドの場合
@[speakerdeck](01c5d829d9e0476b8fbfe634d0353a08)
```

すると、以下のようになりました。

@[speakerdeck](01c5d829d9e0476b8fbfe634d0353a08)

これで、Zennの記事内にスライドを埋め込んで共有することができました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

Speaker Deckはプレゼンテーション資料の共有を効率化してくれる便利なツールです。

興味を持った方は、ぜひ一度Speaker Deckを使ってみてください！

**皆さんも素敵なハッピーSpeaker Deckライフを！！！🌸**
