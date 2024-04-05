---
title: "環境変数ってなんや！JSONってなんや！"
emoji: "💥"
type: "tech"
topics:
  - "json"
  - "mac"
  - "python"
  - "python3"
  - "環境変数"
published: false
---

## はじめに

:::message
この記事は、私の解釈に基づいて環境変数やJSONに関する内容をまとめたものです。
もし誤解や間違いがあれば、ぜひコメントなどでご指摘して頂けると助かります。
:::

以前，私が書いた[記事](https://zenn.dev/joho0724/articles/sankaku0724-newcreate7)を読んでくれた[友人](https://zenn.dev/l085440)から次のような感想をいただきました．

- トークンやチャンネルIDを直接プログラムに記述するべきではない
- 環境変数やJSONなどに設定して読み込ませるべき
- Git使う時とかにトークンとかそのまま上げたらまずいよ

確かにそうかもしれない⋯
でも，環境変数ってなんや！JSONってなんや！全くわからん！

## 環境変数ってなんや？

[Wikipedia](https://ja.wikipedia.org/wiki/%E7%92%B0%E5%A2%83%E5%A4%89%E6%95%B0)によると，次のように説明されていました．

> 環境変数（かんきょうへんすう、英語: environment variable）はオペレーティングシステム (OS) が提供するデータ共有機能の一つ。OS上で動作するタスク（プロセス）がデータを共有するための仕組みである。特にタスクに対して外部からデータを与え、タスクの挙動・設定を変更するために用いる。

どうやら，**環境変数として保存しておけば，すべてのプログラムでその値を使うことができる**ようです．
プログラムの外（PC本体など）に定義できる変数ってイメージがわかりやすそうです．

## 環境変数使ってみた

試しにPythonで環境変数を使ってみました．

以下の記事で紹介しているLINE.pyに記載していたLINE Notifyのアクセストークンを環境変数として設定してみます．

https://zenn.dev/joho0724/articles/sankaku0724-newcreate4

::::details 元々のLINE.py
```py:LINE.py
# requestsモジュールのインポート
import requests

# 送信するメッセージを定義
linemsg = 'xxxxxxxx' # 送信したいメッセージに置き換えてください

# LINE Notifyのアクセストークンを定義
token = "xxxxxxxx" # アクセストークンに置き換えてください

#LINEメッセージ送信の関数
def LINE_message(msg):
  # APIエンドポイントのURLを定義
  url = "https://notify-api.line.me/api/notify"
  # HTTPリクエストヘッダーの設定 
  headers = {"Authorization" : "Bearer "+ token}
  # 送信するメッセージの設定
  message =  (msg)
  # ペイロードの設定
  payload = {"message" :  message}
  # POSTリクエストの使用 
  r = requests.post(url, headers = headers, params=payload)

# 関数の呼び出し
LINE_message(linemsg)
```
::::

### 動作環境

Macbook Air M1
MacOS Sonoma 14.4.1
Python 3.9.6

### python-dotenv

今回は，Pythonプロジェクトで環境変数を管理するためのライブラリである**python-dotenv**を使用してみます。
まず，プロジェクトのルートディレクトリに.envというファイルを作成し、その中に環境変数を記述します。その後、プログラム内でpython-dotenvを使用して.envファイルから環境変数を取得することができます。

モジュールがインストールされていない場合は，以下のコマンドを実行してpython-dotenvをインストールしましょう．

:::message
このコマンドは環境に応じて適切なものを使用してください。
:::

```
pip3 install python-dotenv
```

.envファイルは，作業ディレクトリ内で以下のコマンドを実行することで作成できます．
```
touch .env
```

私が作成した.envファイルは隠しファイルになっていました．セキュリティ上の観点からこのようになっているのかもしれません．

そして，.envファイル内に次のように書き込みました．

```
LINE_NOTIFY_TOKEN='xxxxxxxxxxxxxxxxxx'
```

.envファイルは，一行に一つの環境変数を`変数名=値`の形式で記述することができます。

:::message
この記事のLINE_NOTIFY_TOKENの値は，架空の値です。
:::

そして，LINE.pyを.envファイルから環境変数を取得するように変更しました．

::::details .envファイルから環境変数を取得するように変更したLINE.py
```py:LINE.py
import os
from dotenv import load_dotenv

# .envファイルから環境変数を取得する
load_dotenv()

# requestsモジュールのインポート
import requests

# 送信するメッセージを定義
linemsg = '環境変数！'

# LINE Notifyのアクセストークンを取得
token = os.environ['LINE_NOTIFY_TOKEN']

#LINEメッセージ送信の関数
def LINE_message(msg):
  # APIエンドポイントのURLを定義
  url = "https://notify-api.line.me/api/notify"
  # HTTPリクエストヘッダーの設定 
  headers = {"Authorization" : "Bearer "+ token}
  # 送信するメッセージの設定
  message =  (msg)
  # ペイロードの設定
  payload = {"message" :  message}
  # POSTリクエストの使用 
  r = requests.post(url, headers = headers, params=payload)

# 関数の呼び出し
LINE_message(linemsg)
```
::::

`load_dotenv()`で.envファイルから環境変数を取得し，`os.environ[環境変数名]`で指定した環境変数を呼び出すことができます．

このプログラムを実行すると，正しく動作したことから，LINE Notifyのアクセストークンを正確に取得できていることがわかりました．

![](/images/sankaku8/kankyo.jpg)

これにより，.envを使った環境変数の管理について理解を深めることができました．

## JSONってなんや？

[Wikipedia](https://ja.wikipedia.org/wiki/JavaScript_Object_Notation#cite_note-1)によると，次のように説明されていました．

> JavaScript Object Notation（JSON、ジェイソン）はデータ記述言語の1つである。軽量なテキストベースのデータ交換用フォーマットでありプログラミング言語を問わず利用できる[^1]。名称と構文はJavaScriptにおけるオブジェクトの表記法に由来する。

[^1]: [JSON is a lightweight, text-based, language-independent syntax for defining data interchange formats.](https://ecma-international.org/publications-and-standards/standards/ecma-404/)

**JSONは元々，JavaScript由来のデータ記述言語**であり，JSONファイル内に環境変数を記述することで、プログラムが実行される環境に依存しないようにできるようです．

つまり，大まかな役割は先ほどの.envと同様ということですね．

## JSON使ってみた

JSONファイルは，作業ディレクトリ内で以下のコマンドを実行することで作成できます．

```
touch example.json
```

これにより、カレントディレクトリにexample.jsonという名前の空のJSONファイルが作成されます。
そして，このファイル内に次のように書き込みました．


```json:example.json
{
    "LINE_NOTIFY_TOKEN": "xxxxxxxxxxxxxxxxxx"
}
```

:::message
先ほどと同様に，LINE_NOTIFY_TOKENの値は，架空の値です。
:::

また，どうやらJSON形式では、キーも値も両方で`"`で囲む必要があるようです．
`'`だとエラーを吐きました．

そして，LINE.pyをJSONファイルから環境変数を取得するように変更しました．

::::details example.jsonから環境変数を取得するように変更したLINE.py
```py:LINE.py
# requestsモジュールのインポート
import requests

# 送信するメッセージを定義
linemsg = 'JSONだよ！'

import json

# example.jsonファイルからLINE_NOTIFY_TOKENを読み込む
with open('example.json', 'r') as json_file:
    data = json.load(json_file)
    token = data.get('LINE_NOTIFY_TOKEN')

# LINE_NOTIFY_TOKENが取得できたか確認
if token:
    print("LINE_NOTIFY_TOKEN:", token)
else:
    print("LINE_NOTIFY_TOKENが見つかりませんでした。")

#LINEメッセージ送信の関数
def LINE_message(msg):
  # APIエンドポイントのURLを定義
  url = "https://notify-api.line.me/api/notify"
  # HTTPリクエストヘッダーの設定 
  headers = {"Authorization" : "Bearer "+ token}
  # 送信するメッセージの設定
  message =  (msg)
  # ペイロードの設定
  payload = {"message" :  message}
  # POSTリクエストの使用 
  r = requests.post(url, headers = headers, params=payload)

# 関数の呼び出し
LINE_message(linemsg)
```
::::

`import json`でjsonモジュールをインポートし，LINE.pyと同じディレクトリに配置したexample.jsonからLINE_NOTIFY_TOKENを読み込みます。

このプログラムを実行すると，正しく動作したことから，LINE NotifyのアクセストークンをJSONファイルから正確に取得できていることがわかりました．

![](/images/sankaku8/JSON.jpg)

これにより，JSONを使った環境変数の管理について理解を深めることができました．


## まとめ

.envやJSONを使うことで，プロジェクトの環境ごとに異なる設定を環境変数として，容易に管理することができるということが実感できました。また、環境変数を外部から読み込むことで、セキュリティを向上させることもできるということについても学びを深めることができました。

Pythonだけでなく他のプログラミング言語でも、
.envやJSONファイルを使った環境変数の管理が可能です。そのため、言語を問わずに一貫した方法で環境設定を行うことができます。


.env ファイルと JSON（JavaScript Object Notation）ファイルは、どちらもデータを保存するためのファイル形式ですが、それぞれ独自の目的や特性があります。

.env ファイル:

.env ファイルは、環境変数を設定するためのファイル形式です。主に開発環境やサーバーの設定に使われます。
.env ファイルはテキストファイルで、キーと値のペアを含みます。通常は KEY=VALUE の形式で記述されます。
この形式のファイルは、環境変数を設定することで、アプリケーションがその環境に合わせて動作するようにします。セキュリティ上の理由から、秘密情報を含む場合には .env ファイルを適切に保護する必要があります。
JSON ファイル:

JSON は、データを保存するためのフォーマットとして一般的に使用されます。JavaScript オブジェクトの表現方法に基づいています。
JSON ファイルは、テキストベースで、データをキーと値のペアの集合として表します。JavaScript のオブジェクトと同様の構造を持ちます。
JSON ファイルは、データのシリアル化とデシリアル化に使用されます。Web API との通信、設定ファイル、データの永続化など、さまざまな用途で利用されます。
主な違いは、.env ファイルが環境変数の設定に使用され、主に開発環境での利用に向いているのに対し、JSON ファイルは汎用的なデータの保存に使用され、データの構造化や交換に適していることです。






## 参考記事

https://zenn.dev/nakashi94/articles/9c93b6a58acdb4

https://wa3.i-3-i.info/word11027.html

https://qiita.com/toki_mwc/items/67047e93996c033f5c8e

https://qiita.com/shown_it/items/2b85434e4e2658c484f4

https://note.com/gorojy/n/nbd5a3e2235bd

https://qiita.com/Morio/items/7538a939cc441367070d

https://datamix.co.jp/media/datascience/introduction-to-JSON/#:~:text=JSON%E3%81%A8%E3%81%AFJavaScript%20Object,%E3%81%A6%E9%96%8B%E7%99%BA%E3%81%95%E3%82%8C%E3%81%BE%E3%81%97%E3%81%9F%E3%80%82

https://qiita.com/halglobe0108/items/dc056cc48256eeff5526