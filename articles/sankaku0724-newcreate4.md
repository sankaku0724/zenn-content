---
title: "PythonでLINEに通知を送る"
emoji: "📱"
type: "tech"
topics:
  - "python3"
  - "line"
  - "linenotify"
  - "python"
  - "raspberrypi"
published: true
---

## はじめに
こんにちは。
今回はPythonでLINEに通知を送る方法について記事を書いてみました。
少しでも参考になれば幸いです。

## LINE Notifyのアクセストークンの発行

今回はLINEが提供している公式アカウントである**LINE Notify**を利用します。
LINE Notifyは、LINEと外部のサービスやアプリを連携して通知を受け取ることができるサービスです。
無料で使用できるため、活用までのハードルが低いのも特徴です。
以下のリンクからLINE Notifyのサイトにアクセスすることができます。

https://notify-bot.line.me/ja/

LINEアカウントでログインし、右上にある自身のアカウント名を押して**マイページ**に進みましょう。

![](/images/sankaku4/line1.png)

次に、アクセストークンの発行の「トークンを発行する」を押して進みましょう。

![](/images/sankaku4/line2.png)

すると、トークン名を記入し、通知を送信するトークルームを選択することができます。
ここで、**1:1でLINE Notifyから通知を受け取る**と選択するとLINE Notifyの公式アカウントから通知を受け取ることができます。

![](/images/sankaku4/line3.png)

これによって、アクセストークンを発行することができます。

![](/images/sankaku4/line4.png)

このアクセストークンを利用することでLINEに通知を送ることができるようになるので、発行したアクセストークンを必ず控えておくことを忘れないようにしましょう。

アクセストークンを発行すると、LINE Notifyの公式アカウントからアクセストークンを発行したことを知らせる通知が来ます。

![](/images/sankaku4/line5.png)

もしアクセストークンが外部に流出してしまったり、もう使わなくなったりした場合には同じリンクから削除することもできるため安心です。

![](/images/sankaku4/line6.png)

## PythonでLINEに通知を送ってみよう

LINE.pyというファイルを作成し、以下のように書き込みました。

```py:LINE.py
# requestsモジュールのインポート
import requests

# 送信するメッセージを定義
linemsg = 'Raspberry Piからの通知だよ!'

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

以下に、プログラムの詳細な説明をします。

-----
1. `import requests`: `requests`モジュールをインポートします。このモジュールは、HTTPリクエストを送信するためのPythonライブラリです。

2. `linemsg = 'Raspberry Piからの通知だよ!'`: 送信するメッセージを定義します。ここでは、"Raspberry Piからの通知だよ!"というメッセージが`linemsg`変数に格納されています。

3. `token = "xxxxxxxx"`: LINE Notifyのアクセストークンを`token`変数に格納します。

4. `def LINE_message(msg)`: LINEメッセージを送信するための関数`LINE_message`を定義します。この関数は、引数として`msg`を取ります。

5. `url = "https://notify-api.line.me/api/notify"`: LINE NotifyのAPIエンドポイントのURLを定義します。

6. `headers = {"Authorization" : "Bearer "+ token}`: HTTPリクエストヘッダーを設定します。これには、アクセストークンが含まれています。

7. `message = (msg)`: 送信するメッセージを設定します。引数`msg`から取得します。

8. `payload = {"message" : message}`: 送信するメッセージをペイロードに設定します。

9. `r = requests.post(url, headers = headers, params=payload)`: POSTリクエストを使用してLINE Notifyにメッセージを送信します。`url`はエンドポイント、`headers`はヘッダー、`payload`はデータを指定します。

10. `LINE_message(linemsg)`: 実際に`LINE_message`関数を呼び出し、`linemsg`変数に格納されたメッセージを送信します。

-----

このプログラムをRaspberry Pi上で実行すると、linemsgに格納した文章をLINEに送信することができました。

![](/images/sankaku4/line7.png)

格納する文章を変更することで、[睡蓮花](https://www.youtube.com/watch?v=PjGbnPYwt1g)を送信してみたり⋯

![](/images/sankaku4/line8.png)

真面目にRaspberry Piで取得したセンサ情報を送信してみたり⋯！

![](/images/sankaku4/line9.png)

私はRaspberry Pi上でプログラムを実行しましたが、その他の環境でも実行できるはずです！

## さいごに
ここまで記事を読んでくださりありがとうございました！
送信する情報や関数を呼び出す場所を変更することによって、様々な通知をLINEに送ることができるようになります！
今回、私はPythonを使用しましたが、他の言語で送信するのも面白そうですよね！
**皆さんも素敵なハッピーLINE Notifyライフを！！！🌸**
