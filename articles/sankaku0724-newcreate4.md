---
title: "PythonでLINEに通知を送る方法"
emoji: "📱"
type: "tech"
topics:
  - "python3"
  - "line"
  - "linenotify"
  - "api"
  - "raspberrypi"
published: false
---

## はじめに
こんにちは。
今回は「**PythonでLINEに通知を送る方法**」ということで記事を書いてみました。
少しでも参考になれば幸いです。

## LINE Notifyのアクセストークンの発行

今回はLINEが提供している公式アカウントである**LINE Notify**を利用します。
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

ここで、発行したトークンを必ず控えておくことを忘れないようにしましょう。

トークンを発行すると、LINE Notifyの公式アカウントから通知が来ます。

![](/images/sankaku4/line5.png)

また、発行したトークンは同じリンクから削除することもできるため簡単に作り直すこともできます。

![](/images/sankaku4/line6.png)

## PythonでLINEに通知を送ってみよう

LINE.pyというファイルを作成し、以下の内容のように書き込みました。

```py:LINE
import requests

linemsg = 'Raspberry Piからの通知だよ!'

token = "xxxxxxxx" # アクセストークンに置き換えてください

#LINEメッセージ送信の関数
def LINEsend_message(msg):
    url = "https://notify-api.line.me/api/notify" 
    headers = {"Authorization" : "Bearer "+ token}
    message =  (msg)
    payload = {"message" :  message} 
    r = requests.post(url, headers = headers, params=payload)

LINEsend_message(linemsg)
```
そして、Raspberry Pi上でこのプログラムを実行すると、linemsgに格納した文章をLINEに送信することができました。

![](/images/sankaku4/line7.png)

他にもこんな文章を送信してみたり⋯

![](/images/sankaku4/line8.png)

真面目にRaspberry Piで取得したセンサ情報を送信してみたり⋯！

![](/images/sankaku4/line9.png)

私はRaspberry Pi上でプログラムを実行しましたが、その他の環境でも実行できるはずです！

## さいごに
ここまで記事を読んでくださりありがとうございました！
linemsgに格納する情報やLINEsend_message関数を呼び出す場所によって、様々な通知をLINEに送ることができるようになります！

**皆さんも素敵なハッピーLINE Notifyライフを！！！🌸**