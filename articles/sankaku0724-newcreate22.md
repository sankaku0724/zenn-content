---
title: "【C言語】動的メモリ関数紹介"
emoji: "🥸"
type: "tech"
topics:
  - "c言語"
  - "mac"
  - "メモリ"
  - "プログラミング"
  - "初心者"
published: false
---

## はじめに

今回は、C言語で動的メモリを扱う際によく用いる関数についてまとめてみました。

:::message
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- MacOS Sonoma 14.5
- Apple clang version 15.0.0 (clang-1500.3.9.4)

## メモリ確保

今回は、以下の関数の説明と簡単な使用例について紹介します。

1. **malloc**
2. **calloc**
3. **alloca**
4. **realloc**

### 1. malloc

malloc関数は、**指定したサイズのメモリをヒープ領域に動的に確保する関数**です。
使用する際には、ヘッダーファイル「stdlib.h」をインクルードする必要があります。
また、**割り当てられたメモリは0で初期化されない**という特徴があります。
**メモリの確保に成功した場合にはvoid型のポインタを返し、失敗した場合にはNULLを返します。**
また、確保したメモリ領域は**不要になったら必ずfree関数で解放することが重要です。**

```c
ポインタ変数 = malloc(必要なメモリのバイト数);
```

以下に、malloc関数の使用例として、「malloc関数例を用いて、5個の整数を格納するのに十分なメモリ領域を動的に確保し、確保されたメモリ領域の各要素を表示するプログラム」を示します。

:::details malloc関数の使用例

```c:malloc.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *arr;
    int n = 5;

    // メモリの動的割り当て
    arr = (int *)malloc(n * sizeof(int));

    // メモリの割り当てが成功したか確認
    if (arr == NULL) {
        printf("Memory not allocated.\n");
        return 1;
    }

    // 配列の値を表示
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    // 最後に改行を追加
    printf("\n");

    // メモリの解放
    free(arr);

    return 0;
}
```

実行すると、**割り当てられたメモリは0で初期化されない**ため、ランダムな値が表示されます。

![](/images/sankaku22/malloc1.png)
*実行結果1*

また、この値は実行する度に変わります。

![](/images/sankaku22/malloc2.png)
*実行結果2*

:::

### 2. calloc

calloc関数は、ほとんどmalloc関数と同じ機能を持っています。

しかし、**malloc関数と異なる点として、割り当てられたメモリは0で初期化される**という特徴があります。

```c:
ポインタ変数 = calloc(要素の数, 要素のバイト数);
```

以下に、calloc関数の使用例として、「calloc関数例を用いて、5個の整数を格納するのに十分なメモリ領域を動的に確保し、確保されたメモリ領域の各要素を表示するプログラム」を示します。

:::details calloc関数の使用例

```c:calloc.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *arr;
    int n = 5;

    // メモリの動的割り当て
    arr = (int *)calloc(n, sizeof(int));

    // メモリの割り当てが成功したか確認
    if (arr == NULL) {
        printf("Memory not allocated.\n");
        return 1;
    }

    // 配列の値を表示（すべてゼロで初期化されている）
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }

    // 最後に改行を追加
    printf("\n");

    // メモリの解放
    free(arr);

    return 0;
}
```

実行すると、**割り当てられたメモリは0で初期化される**ため、確保した領域には0が表示されます。

![](/images/sankaku22/calloc.png)
*実行結果*

:::

### 3. alloca

alloca関数は、**指定したサイズのメモリをスタック領域に動的に確保する関数**です。
使用する際には、ヘッダーファイル「alloca.h」をインクルードする必要があります。
スタック上にメモリを確保するため、malloc関数とは異なり、NULLを返すことはありません。呼び出し元の型に合わせた適切なポインタが返されます。
また、確保したメモリ領域は関数が終了すると自動的に解放されるため、**free関数を呼び出す必要がありません。**

```c
ポインタ変数 = alloca(必要なメモリのバイト数);
```

以下に、alloca関数の使用例として、「alloca関数例を用いて、5個の整数を格納するのに十分なメモリ領域を動的に確保し、確保されたメモリ領域の各要素を表示するプログラム」を示します。

:::details alloca関数の使用例

```c:alloca.c
#include <stdio.h>
#include <alloca.h>

int main() {
    int n = 5;

    // スタック上にメモリを割り当て
    int *arr = (int *)alloca(n * sizeof(int));

    // 配列に値を設定
    for (int i = 0; i < n; i++) {
        arr[i] = i + 1;
    }

    // 配列の値を表示
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
	// 最後に改行を追加
    printf("\n");

    // `alloca`で割り当てられたメモリは手動で解放する必要がない

    return 0;
}
```

実行すると、5個の整数を確保できるメモリ領域が確保できていることがわかります。

![](/images/sankaku22/alloca.png)
*実行結果*

:::

### 4. realloc

realloc関数は、**既存のメモリブロックのサイズを変更する関数**です。
使用する際には、ヘッダーファイル「stdlib.h」をインクルードする必要があります。
また、**割り当てられたメモリは0で初期化されない**という特徴があります。
**メモリの確保に成功した場合には新しいメモリブロックへのポインタを返します。**
失敗した場合には**NULLを返し、元のメモリブロックは維持されます。**

```c
ポインタ変数 = realloc(ptr, size);
```

ここで

- ptr: 変更したいメモリブロックのポインタ
- size: 新しいメモリブロックのサイズ

となっており、**ptrがNULLの場合はmalloc関数と同等の動作**をします。
また、**sizeが0の場合はfree関数と同等の動作**をします。

以下に、realloc関数の使用例として、「malloc関数で確保した5個の整数を格納するのに十分なメモリ領域を、realloc関数で10に拡張するプログラム」を示します。

:::details realloc関数の使用例

```c:realloc.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *arr;
    int n = 5;
    int new_size = 10;

    // メモリの動的割り当て
    arr = (int *)malloc(n * sizeof(int));

    // メモリの割り当てが成功したか確認
    if (arr == NULL) {
        printf("Memory not allocated.\n");
        return 1;
    }

    // 配列に値を設定
    for (int i = 0; i < n; i++) {
        arr[i] = i + 1;
    }

	// 配列の値を表示
	printf("Before\n");
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    // 最後に改行を追加
    printf("\n");

    // メモリブロックの再割り当て
    arr = (int *)realloc(arr, new_size * sizeof(int));

    // 新しいサイズのメモリの割り当てが成功したか確認
    if (arr == NULL) {
        printf("Memory not reallocated.\n");
        return 1;
    }

    // 配列の新しい部分に値を設定
    for (int i = n; i < new_size; i++) {
        arr[i] = i + 1;
    }

    // 配列の値を表示
	printf("After\n");
    for (int i = 0; i < new_size; i++) {
        printf("%d ", arr[i]);
    }

	// 最後に改行を追加
    printf("\n");

    // メモリの解放
    free(arr);

    return 0;
}
```

実行すると、まず初めにmalloc関数で確保した5個の整数を確保できるメモリ領域が確保できていることがわかります。

また、realloc関数で10個の整数を格納するのに十分なメモリ領域に拡張できていることもわかります。

![](/images/sankaku22/realloc.png)
*実行結果*

:::

このような関数を扱うことで、C言語で動的にメモリを割り当てて使用することができます。

## さいごに

ここまで記事を読んでくださり、ありがとうございました！今回は、C言語で動的メモリを扱う際によく用いる関数についてまとめてみました。

**皆さんも素敵なハッピーC言語ライフを！！！🌸**
