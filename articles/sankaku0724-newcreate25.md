---
title: "PythonでZennの画像埋め込みリンクを生成"
emoji: "🐍"
type: "tech"
topics:
  - "python"
  - "zenn"
  - "python3"
  - "アウトプット"
  - "markdown"
published: false
---

## はじめに

Zennで記事を執筆する際に、画像を載せたい時は以下のように画像埋め込みリンクを記述する必要があります。

```md
![](画像のURLまたはファイルパス)
```

載せたい画像が多い時、一つ一つ記述するのは面倒です。そこで、とても簡単なPythonプログラムを作成し、執筆の効率を上げることにしました。

## 私の環境

私はZennの記事を[ローカルのテキストエディター + CLIで執筆している](https://zenn.dev/zenn/articles/editor-guide#2.-%E3%83%AD%E3%83%BC%E3%82%AB%E3%83%AB%E3%81%AE%E3%83%86%E3%82%AD%E3%82%B9%E3%83%88%E3%82%A8%E3%83%87%E3%82%A3%E3%82%BF%E3%83%BC-%2B-cli)のですが、Pythonプログラムの実装にあたり、画像に番号をつけて管理することにします。

画像のパスを確認してみましょう。

![](/images/sankaku25/1.png)
*「articles」フォルダと同じディレクトリに「images」フォルダが存在*

ここで、「articles」フォルダの中には、Markdown形式で執筆された（もしくは執筆中の）記事が存在しています。

![](/images/sankaku25/3.png)
*「images」フォルダの中には記事番号をつけて管理しているフォルダが存在*

![](/images/sankaku25/5.png)
*画像は番号をつけて管理*

画像は載せたい順に1から番号をつけています。

つまり、私の場合は以下のように記述すれば良いわけです。

```md:私の場合
![](/images/sankaku[記事番号]/[画像番号].png)
```

## Pythonプログラム

以下のように、「number.py」というPythonプログラムを作成しました。

```py:number.py
number = int(input("記事番号の入力："))
count = int(input("画像の枚数の入力："))

for i in range(1, count + 1):
    print("![](/images/sankaku" + str(number) + "/" + str(i) + ".png)\n")
```

これを実行すると、記事番号と画像の枚数の入力を求められます。
例えば、記事番号8に9枚の画像を載せたい時の実行結果が以下になります。

![](/images/sankaku25/2.png)

記事を執筆する際は、これをコピペすれば良いわけです。
Markdown形式の画像埋め込みリンクを生成するのが少し楽になりました！

![](/images/sankaku25/4.png)
*この記事も「number.py」で生成した画像埋め込みリンクを使って執筆されています！*

Pythonに嫌悪感を持っている方もいるかもしれませんが、シンプルに記述できるので私は大好きです。**Pythonは良いですよ！**

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

**[アウトプットは素晴らしい](https://zenn.dev/joho0724/articles/sankaku0724-newcreate6)もの**です。

この記事で紹介した方法以外のことでも、創意工夫を凝らしてアウトプットのハードルを下げることが大切です！

**皆さんも素敵なハッピーZennライフを！！！🌸**
