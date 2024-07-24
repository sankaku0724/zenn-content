---
title: "【C言語】めざせ動的メモリマスター"
emoji: "👾"
type: "tech"
topics:
  - "c言語"
  - "c"
  - "メモリ"
  - "プログラミング"
  - "初心者"
published: false
---

## はじめに

今回は、私がC言語で動的メモリを扱う際によく用いる関数についてまとめてみました。

:::message
もし誤解や間違いがあれば、ぜひコメントなどでご指摘していただけると助かります。
:::

### 私の動作環境

- MacOS Sonoma 14.5
- Apple clang version 15.0.0 (clang-1500.3.9.4)

## 動的メモリ割り当て関数

今回は、以下の関数の説明と簡単な使用例について紹介します。

1. **malloc**（memory allocation）
2. **calloc**（contiguous allocation）
3. **alloca**（allocate on the stack）
4. **realloc**（re-allocation）

ここで、**`allocation`は割り当てという意味**です。

### 1. malloc

mallocは、**指定したサイズのメモリをヒープ領域に動的に確保する関数**です。
使用する際には、ヘッダーファイル「stdlib.h」をインクルードする必要があります。
また、**割り当てられたメモリは0で初期化されない**という特徴があります。
**メモリの確保に成功した場合にはvoid型のポインタを返し、失敗した場合にはNULLを返します。**
ここで重要なポイントとして、確保したメモリ領域は**不要になったら必ずfree関数で解放する必要があります。**

```c
ポインタ変数 = malloc(必要なメモリのバイト数);
```

#### 使用例

以下にmallocの使用例として、**5個の整数を格納するのに十分なメモリ領域を動的に確保し、確保されたメモリ領域の各要素を表示するプログラム**を示します。

:::details mallocの使用例

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

callocは、ほとんどmallocと同じ機能を持っています。

しかし、mallocと異なる点として、**割り当てられたメモリは0で初期化される**という特徴があります。

```c:
ポインタ変数 = calloc(要素の数, 要素のバイト数);
```

#### 使用例

以下にcallocの使用例として、**5個の整数を格納するのに十分なメモリ領域を動的に確保し、確保されたメモリ領域の各要素を表示するプログラム**を示します。

:::details callocの使用例

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

#### mallocとcallocの使い分け

では、mallocとcallocはどのように使い分けると良いのでしょうか。

速度の面で見ると、mallocの方が高速です。なぜなら、callocは初期化する処理が必要になってくるからです。
パフォーマンスが重要な場合や、割り当てたメモリにすぐに値を書き込む場合にはmallocを使用すると良いでしょう。

初期値として0が必要な場合や、安全性を重視する場合（未初期化メモリによるバグを防ぐなど）、複雑なデータ構造（配列や構造体など）を扱う場合にはcallocの方が適しています。

### 3. alloca

allocaは、**指定したサイズのメモリをスタック領域に動的に確保する関数**です。
使用する際には、ヘッダーファイル「alloca.h」をインクルードする必要があります。
スタック上にメモリを確保するため、mallocとは異なり、NULLを返すことはありません。呼び出し元の型に合わせた適切なポインタが返されます。
また、確保したメモリ領域は関数が終了すると自動的に解放されるため、**free関数を呼び出す必要がありません。**
スタックオーバーフローには注意する必要があります。

```c
ポインタ変数 = alloca(必要なメモリのバイト数);
```

#### 使用例

以下にallocaの使用例として、**5個の整数を格納するのに十分なメモリ領域を動的に確保し、確保されたメモリ領域の各要素を表示するプログラム**を示します。

:::details allocaの使用例

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

reallocは、**既存のメモリブロックのサイズを変更する関数**です。
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

となっており、**ptrがNULLの場合はmallocと同等の動作**をします。
また、**sizeが0の場合はfree関数と同等の動作**をします。

#### 使用例

以下にreallocの使用例として、**mallocで確保した5個の整数を格納するのに十分なメモリ領域を、reallocで10個の整数を格納できるように拡張するプログラム**を示します。

:::details reallocの使用例

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
    int *temp = (int *)realloc(arr, new_size * sizeof(int));

    // 新しいサイズのメモリの割り当てが成功したか確認
    if (temp == NULL) {
        printf("Memory not reallocated.\n");
        free(arr);  // 元のメモリを解放
        return 1;
    }
    arr = temp;

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

実行すると、まず初めにmallocで確保した5個の整数を確保できるメモリ領域が確保できていることがわかります。

また、reallocで10個の整数を格納するのに十分なメモリ領域に拡張できていることも確認できました。

![](/images/sankaku22/realloc.png)
*実行結果*

:::

このような関数を扱うことで、C言語で動的にメモリを割り当てて使用することができます。

## さいごに

ここまで記事を読んでくださり、ありがとうございました！この記事で、適切に動的メモリを扱う手助けができれば幸いです！
**皆さんも素敵なハッピーC言語ライフを！！！🌸**
