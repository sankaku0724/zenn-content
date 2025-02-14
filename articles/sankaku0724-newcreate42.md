---
title: "【KMP】LazyRowのクラッシュ問題を解決する"
emoji: "🐢"
type: "tech"
topics:
  - "android"
  - "kotlin"
  - "kmp"
  - "androidstudio"
  - "ui"
published: true
published_at: 2025-02-22 12:00
publication_name: "cocban_blog"
---

## はじめに

Android Studioで`Kotlin Multiplatform(KMP)`を使用して開発した際に、`ScrollableTabRow`内に`LazyRow`を配置すると**アプリがクラッシュする問題**が発生しました。

:::details 実行例
```kotlin:※実際の開発で使用しているものではありません
@Composable
fun ExampleTabRow(
    coroutineScope: CoroutineScope,
    pagerState: PagerState,
    tabItems: List<String>
) {
    Column {
        ScrollableTabRow(
            selectedTabIndex = pagerState.currentPage,
            edgePadding = 0.dp,
            indicator = { tabPositions ->
                Box(
                    Modifier
                        .tabIndicatorOffset(tabPositions[pagerState.currentPage])
                        .height(3.dp)
                        .background(color = Color(0xFF007993))
                )
            }
        ) {
            LazyRow(
                modifier = Modifier.fillMaxSize()
            ) {
                itemsIndexed(tabItems) { index, title ->
                    TabItem(
                        selected = pagerState.currentPage == index,
                        text = title,
                        onClick = { coroutineScope.launch { pagerState.scrollToPage(index) } },
                    )
                }
            }
        }
    }
}
```
:::

この記事は、私がその問題を解決した方法について紹介するものになります。

:::message
本記事で紹介する方法は、あくまで一例として提供しています。
**適用については、ご自身の環境に合わせて十分に調査してご判断ください**。
なお、これらの手順を実行したことによる結果や影響について、筆者は一切の責任を負いかねますので、ご了承ください。
:::

## 結論

**レイアウトの競合**が発生しています。
`ScrollableTabRow`内に`LazyRow`ではなく別のものを配置しましょう。

## 説明

`ScrollableTabRow`と`LazyRow`はどちらもスクロール可能なコンポーネントです。
そのため、`ScrollableTabRow`の内部で`LazyRow`を使用するとレイアウトの競合が発生します。

これだけだと分かりにくいかもしれないので雑に例えてみます。
遊園地に「列に並んで乗るアトラクション」があるとした場合、「ジェットコースター🎢（`ScrollableTabRow`）」に「観覧車🎡（`LazyRow`）」を乗せると大混乱が起きますよね。

解決するには、ジェットコースターにそのまま並べば良いだけの話なのです。
今回、私は`LazyRow`を使わずに`forEachIndexed`で一つずつ`TabItem`を追加することで、正常に動作するように修正しました。

:::details 修正後
```kotlin:※実際の開発で使用しているものではありません
@Composable
fun ExampleTabRow(
    coroutineScope: CoroutineScope,
    pagerState: PagerState,
    tabItems: List<String>
) {
    Column {
        ScrollableTabRow(
            selectedTabIndex = pagerState.currentPage,
            edgePadding = 0.dp,
            indicator = { tabPositions ->
                Box(
                    Modifier
                        .tabIndicatorOffset(tabPositions[pagerState.currentPage])
                        .height(3.dp)
                        .background(color = Color(0xFF007993))
                )
            }
        ) {
            tabItems.forEachIndexed { index, title ->
                TabItem(
                    selected = pagerState.currentPage == index,
                    text = title,
                    onClick = { coroutineScope.launch { pagerState.scrollToPage(index) } },
                )
            }
        }
    }
}
```
:::

レイアウトを組む時は、各コンポーネントを適切に使用しましょう！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

UIを実装する時は各コンポーネントのスクロール特性を理解することが大事ですね。

**皆さんも素敵なハッピーKMPライフを！！！🌸**

