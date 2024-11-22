---
title: "【KMP】Xcodeのビルドで発生するSQLite3エラーの対処法"
emoji: "🛠️"
type: "tech"
topics:
  - "swift"
  - "swiftui"
  - "kmp"
  - "xcode"
  - "ios"
published: true
published_at: 2024-11-30 12:00
publication_name: "cocban_blog"
---

## はじめに

Android Studioで`Kotlin Multiplatform(KMP)`を使用してiOSアプリを作成し、Xcodeでビルドした際、「`Undefined symbol: _sqlite3_bind_blob`」などの**SQLite3に関する多数のエラーが発生してビルドができなくなる問題**が発生しました。

![](/images/sankaku41/1.png =200x)
*エラーの一部*

この記事は、私がその問題を解決した方法について紹介するものになります。

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

**XcodeプロジェクトのBuild Settingsで、Other Linker Flagsに「`-lsqlite3`」を追加**すると解決できました。

## 手順

[似たような状況に直面された方](https://github.com/sqldelight/sqldelight/issues/1442#issuecomment-523435492)がいたので、その解決方法を参考にさせていただきました。
このエラーは、**プロジェクトが`sqlite3`ライブラリを正しくリンクしていないことが原因**のようです。

まず、Xcode内でBuild Settingsタブを開きます。
Build Settingsタブは左側のプロジェクトナビゲータ（一番左のサイドバー）を開き、青色のプロジェクトアイコンを選択すると開くことができます。

![](/images/sankaku41/2.png =200x)
*私の場合は赤い四角で囲んだ部分を選択*

Build Settingsタブを開いたら、検索バーで「Other Linker Flags」と検索しましょう。
Other Linker FlagsはXcodeのビルド設定の一つで、プロジェクトのリンク処理を設定することができます。

![](/images/sankaku41/4.png)
*検索バーに入力*

Other Linker Flagsを発見したら、ダブルクリックして編集モードに入り、以下のように記述を追加して`sqlite3`ライブラリをリンクしましょう。
また、ここで「指定された名前のライブラリをリンクする」という意味を持つリンカフラグである`-l`オプションを利用します。

```
-lsqlite3
```

![](/images/sankaku41/3.png)
*Other Linker Flagsに`-lsqlite3`を追加*

私はこの作業を行った後、エラーが出ることなく正常にビルドできるようになりました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

この記事が少しでも皆様の手助けになることができれば幸いです！

**皆さんも素敵なハッピーKMPライフを！！！🌸**
