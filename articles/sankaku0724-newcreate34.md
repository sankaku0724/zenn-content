---
title: "KMPで「import shared」が機能しない時の対処法"
emoji: "🤠"
type: "tech"
topics:
  - "swift"
  - "swiftui"
  - "kmp"
  - "java"
  - "jdk"
published: false
---

## はじめに

Android Studioで`Kotlin Multiplatform(KMP)`を使用してiOSアプリを作成し、Xcodeでそのアプリを開いた際に`import shared`でモジュールが正しくインポートされず、エラーが発生してしまいました。

```swift:ContentView（2行目でエラー発生）
import SwiftUI
import shared

struct ContentView: View {
	let greet = Greeting().greet()

	var body: some View {
		Text(greet)
	}
}

struct ContentView_Previews: PreviewProvider {
	static var previews: some View {
		ContentView()
	}
}
```

この記事は、私がその問題を解決した方法をまとめたものになります。

:::message
本記事で紹介する方法は、あくまで一例として提供しています。適用については、ご自身の環境に合わせて十分に調査してご判断ください。なお、これらの手順を実行したことによる結果や影響について、筆者は一切の責任を負いかねますので、ご了承ください。
:::

### 私の動作環境
- MacBook Air M1 2020
- メモリ 16GB
- MacOS Sonoma 14.7

## 結論

**[Java公式](https://www.oracle.com/jp/java/technologies/downloads/#jdk23-mac)からJDK(Java Development Kit)をインストールし、`JAVA_HOME`環境変数を適切に設定した**ところ、問題が解消されました。

## 説明

`Kotlin Multiplatform`は`Java Virtual Machine(JVM)`で動作するKotlinコードを使用します。
そのため、**KMPプロジェクトのビルドにはJDKが必要**です。

まず、Oracle公式サイトからJDKをダウンロードしました。
私は「ARM64 DMG Installer」を選択しましたが、ご自身の環境に合わせてご判断ください。

https://www.oracle.com/jp/java/technologies/downloads/#jdk23-mac

ダウンロードし、インストールまで完了したらパスを設定します。
**私は`JAVA_HOME`環境変数にJDKのパスを追加**しました。
`JAVA_HOME`は、JavaベースのツールがどのJDKを使用すべきかを指定するための環境変数です。

```:Zshの設定ファイルの編集コマンド(別にvimじゃなくても良いです)
vim .zshrc
```

```:私の追加したパス
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-23.jdk/Contents/Home
```

パスを通して、ビルドしてみると以下のような画面になりました。

![](/images/sankaku34/1.jpg)
*ビルド成功*

これによって、KMPプロジェクトのビルドに成功しました！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

KMPは強力なツールですが、適切な設定と理解が必要です。私も今回の学びを活かし、効率的かつ効果的な開発をしていきたいです！

**皆さんも素敵なハッピーKMPライフを！！！🌸**
