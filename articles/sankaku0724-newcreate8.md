---
title: "環境変数ってなんや！JSONってなんや！"
emoji: "😵"
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

以前、私が書いた[記事](https://zenn.dev/joho0724/articles/sankaku0724-newcreate7)を読んでくれた[友人](https://zenn.dev/l085440)から次のような感想をいただきました。

- トークンやチャンネルIDを直接プログラムに記述するべきではない
- 環境変数やJSONなどに設定して読み込ませるべき
- Git使う時とかにトークンとかそのまま上げたらまずいよ

確かにそうかもしれない⋯
でも、環境変数ってなんや！JSONってなんや！全くわからん！

## 環境変数ってなんや？

[Wikipedia](https://ja.wikipedia.org/wiki/%E7%92%B0%E5%A2%83%E5%A4%89%E6%95%B0)によると、次のように説明されていました。

> 環境変数（かんきょうへんすう、英語: environment variable）はオペレーティングシステム (OS) が提供するデータ共有機能の一つ。OS上で動作するタスク（プロセス）がデータを共有するための仕組みである。特にタスクに対して外部からデータを与え、タスクの挙動・設定を変更するために用いる。

どうやら、**環境変数として保存しておけば、すべてのプログラムでその値を使うことができる**ようです。（普何気なく通してるPATHとかもその一種らしい。）
プログラムの外（PC本体など）に定義できる変数ってイメージがわかりやすそう（？）

## 環境変数使ってみた

試しにPythonで環境変数を使ってみました。

以下の記事で紹介しているLINE.pyに記載していたLINE Notifyのアクセストークンを環境変数として設定してみます。

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

今回は、Pythonプロジェクトで環境変数を管理するためのライブラリである**python-dotenv**を使用してみます。
まず、環境変数を保存するための`.env`というファイルを作成し、その中に環境変数を記述します。その後、プログラム内でpython-dotenvを使用して.envファイルから環境変数を取得します。

モジュールがインストールされていない場合は、以下のコマンドを実行してpython-dotenvをインストールしましょう。

:::message
このコマンドは環境に応じて適切なものを使用してください。
:::

```
pip3 install python-dotenv
```

.envファイルは、作業ディレクトリ内で以下のコマンドを実行することで作成できます。
```
touch .env
```

私が作成した.envファイルは隠しファイルになっていました。セキュリティ上の観点からこのようになっているのかもしれません。

そして、.envファイル内に次のように書き込みました。

```
LINE_NOTIFY_TOKEN='xxxxxxxxxxxxxxxxxx'
```

.envファイルは、一行に一つの環境変数を`変数名=値`の形式で記述することができます。

:::message
この記事のLINE_NOTIFY_TOKENの値は、架空の値です。
:::

そして、LINE.pyを.envファイルから環境変数を取得するように変更しました。

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

`load_dotenv()`で.envファイルから環境変数を取得し、`os.environ[環境変数名]`で指定した環境変数を呼び出します。

このプログラムを実行すると正しく動作したことから、LINE Notifyのアクセストークンを正確に取得できていることがわかりました。

![](/images/sankaku8/kankyo.jpg)

これにより、.envを使った環境変数の管理について理解を深めることができました。
セキュリティ上の理由から、秘密情報を含む場合には.envファイルを適切に保護する必要があるでしょう。

## JSONってなんや？

[Wikipedia](https://ja.wikipedia.org/wiki/JavaScript_Object_Notation#cite_note-1)によると、次のように説明されていました。

> JavaScript Object Notation（JSON、ジェイソン）はデータ記述言語の1つである。軽量なテキストベースのデータ交換用フォーマットでありプログラミング言語を問わず利用できる[^1]。名称と構文はJavaScriptにおけるオブジェクトの表記法に由来する。

[^1]: [JSON is a lightweight, text-based, language-independent syntax for defining data interchange formats.](https://ecma-international.org/publications-and-standards/standards/ecma-404/)

**JSONは元々、JavaScript由来のデータ記述言語**であり、JSONファイル内に環境変数などのデータを記述することで、プログラムが実行される環境に依存しないようにできるようです。
つまり、大まかな役割は先ほどの.envと同様ということですが、**JSONファイルは環境変数以外にも、汎用的なデータの保存に使用され、データの構造化や交換に適している**という特徴があるようです。

## JSON使ってみた

動作環境は、python-dotenvの時と同様です。

JSONファイルは、作業ディレクトリ内で以下のコマンドを実行することで作成できます。

```
touch example.json
```

これにより、example.jsonという名前の空のJSONファイルが作成されます。
そして、このファイル内に次のように書き込みました。

```json:example.json
{
    "LINE_NOTIFY_TOKEN": "xxxxxxxxxxxxxxxxxx"
}
```
Pythonの辞書と似た構造でデータを格納することができました。（あくまでも見た目が似ているだけ全くの別物のようですが。）
:::message
どうやらJSON形式では、キーも値も両方`"`で囲む必要があるようです。
試しに`'`で囲んでみるとエラーを吐きました。
:::

そして、LINE.pyをJSONファイルから環境変数を取得するように変更しました。

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

`import json`でjsonモジュールをインポートし、LINE.pyと同じディレクトリに配置したexample.jsonからLINE_NOTIFY_TOKENを取得するようにしました。

このプログラムを実行すると正しく動作したことから、LINE NotifyのアクセストークンをJSONファイルから正確に取得できていることがわかりました。

![](/images/sankaku8/JSON.jpg)

これにより、JSONを使った環境変数の管理について理解を深めることができました。

今回は環境変数を格納しましたその他のデータを外部参照する際にもJSONは役立つでしょう。
## まとめ

今回、実際に作業した中で、以下のことが分かりました。

1. **環境変数として保存しておけば、すべてのプログラムでその値を利用することができる。**

2. **JSONファイルは、主に汎用的なデータの保存に使用され、データの構造化や交換に適している。**

3. **.envやJSONなどのファイルを利用することで、簡単に環境変数の管理ができる。**

4. **重要な情報を環境変数として扱うことで、セキュリティを向上させることができる。**

今回、私はPythonで作業をしましたが、他のプログラミング言語でも、.envやJSONファイル、その他のファイルを用いた環境変数の管理は可能です。
そのため、使用する言語や環境に応じて、自身に合ったものを利用していく必要があると言えるでしょう。

これから、外部に流出してはいけない情報は環境変数として適切に扱っていこうと思います。

**皆さんも素敵なハッピー環境変数ライフを！！！🌸**

## 参考記事

https://zenn.dev/nakashi94/articles/9c93b6a58acdb4

https://wa3.i-3-i.info/word11027.html

https://qiita.com/toki_mwc/items/67047e93996c033f5c8e

https://qiita.com/shown_it/items/2b85434e4e2658c484f4

https://note.com/gorojy/n/nbd5a3e2235bd

https://qiita.com/Morio/items/7538a939cc441367070d

https://datamix.co.jp/media/datascience/introduction-to-JSON/#:~:text=JSON%E3%81%A8%E3%81%AFJavaScript%20Object,%E3%81%A6%E9%96%8B%E7%99%BA%E3%81%95%E3%82%8C%E3%81%BE%E3%81%97%E3%81%9F%E3%80%82

https://qiita.com/halglobe0108/items/dc056cc48256eeff5526
