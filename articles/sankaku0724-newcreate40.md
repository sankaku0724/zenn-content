---
title: "KMPで「import shared」が機能しない時の対処法Ⅱ"
emoji: "🤝"
type: "tech"
topics:
  - "swift"
  - "swiftui"
  - "kmp"
  - "xcode"
  - "kotlin"
published: true
published_at: 2024-11-10 12:00
publication_name: "cocban_blog"
---

## はじめに

Android Studioで`Kotlin Multiplatform(KMP)`を使用してiOSアプリを作成し、Xcodeでそのアプリを開いた際に`import shared`でモジュールが正しくインポートされず、「`No such module 'shared'`」というエラーが発生してしまいました。

![](/images/sankaku34/error.png)

![](/images/sankaku34/error2.png)

一度は「[**KMPで「import shared」が機能しない時の対処法**](https://zenn.dev/cocban_blog/articles/sankaku0724-newcreate34)」という記事に記載している方法で問題を解消したのですが、再び同じエラーが発生してしまいました。

この記事は、私が[CocBan](https://www.cocban.com/)開発メンバー達とその問題を解決した方法をまとめたものになります。

:::message
本記事で紹介する方法は、あくまで一例として提供しています。
**適用については、ご自身の環境に合わせて十分に調査してご判断ください**。
なお、これらの手順を実行したことによる結果や影響について、筆者は一切の責任を負いかねますので、ご了承ください。
:::

### 私の動作環境
- MacBook Air M1 2020
- メモリ 16GB
- MacOS Sonoma 14.7

## 結論

`shared`ディレクトリ内の`build.gradle.kts`ファイルに記述されている`co.touchlab.skie`プラグインのバージョンを以下のように更新することで解決しました。

```kts:build.gradle.kts
plugins {
    id("co.touchlab.skie") version "0.9.3"
}
```

## 説明

[**`co.touchlab.skie`**](https://skie.touchlab.co/)は、KotlinからSwiftへのAPI公開を強化するためのGradleプラグインです。
iOSとAndroidの間でコードを共有する環境を整える目的で利用され、依存関係の管理やモジュールの構成、ビルドプロセスのサポートなどの機能を提供してくれます。
また、SKIEは「Swift Kotlin Interface Enhancer」の略称です。

このバージョンを上げることで、**依存関係の処理やモジュールのリンクのサポートの問題を改善してくれる**可能性があります。

これによって、私はKMPプロジェクトのビルドに成功しました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

この記事が皆さんのKMP開発の手助けになることができれば幸いです！

**皆さんも素敵なハッピーKMPライフを！！！🌸**
