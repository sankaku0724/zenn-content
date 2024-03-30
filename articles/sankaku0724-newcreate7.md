---
title: "Discord Botにおうむ返しさせる"
emoji: "🦜"
type: "tech"
topics:
  - "python3"
  - "python"
  - "discord"
  - "discordbot"
  - "raspberrypi"
published: false
---

## はじめに

今回は「**Discord Botにおうむ返しさせる**」方法について記事を書いてみました。
拙い文章ではありますが、よろしくお願いします。

Discord Botの作成方法などについては、私が前回投稿した記事を参考にしてもらえると嬉しいです。

https://zenn.dev/joho0724/articles/sankaku0724-newcreate3

## PythonでDiscord Botを動かしておうむ返しできるようにしてみよう

Echo.pyというPythonファイルを作成し、以下のように書き込みました。

```py:Echo.py
import discord  # Discordライブラリをインポート

channel_id = xxxxxxx  # 送信先のチャンネルIDに置き換えてください
token = 'xxxxxxx'  # Botのトークンに置き換えてください

intents = discord.Intents.default()  # デフォルトのインテントを使用
intents.message_content = True  # メッセージ内容の受信を有効化
intents.messages = True  # メッセージの受信を有効化

client = discord.Client(intents=intents)  # クライアントを作成

@client.event
async def on_ready():  # Botが準備完了したときのイベント
    print('ログインしました')

@client.event
async def on_message(message):  # メッセージを受信したときのイベント
    if message.author.bot:  # メッセージを送ったのがBot自身なら処理をスキップ
        return

    if message.content.strip():  # メッセージが空でない場合
        try:
            channel = await client.fetch_channel(channel_id)  # チャンネルを取得
            print(f"メッセージを受信しました: {message.content}")
            print(f"チャンネル {channel.name} にメッセージを送信しようとしています: {message.content}")

            send_task = channel.send(f"{message.author.name} からのメッセージ: {message.content}")  # メッセージを送信
            print("送信タスクを開始しました")

            await send_task  # 送信タスクが完了するまで待機
            print("送信タスクが完了しました")

            print(f"メッセージをチャンネル {channel.name} に送信しました: {message.content}")

        except Exception as e:  # エラーが発生した場合
            print(f"エラー: {e}")

client.run(token)  # Botを起動
```

以下に、プログラムの詳細な説明をします。

-----
1. DiscordライブラリをPythonコード内で利用できるようにインポートします。

```py
import discord  # Discordライブラリをインポート（Discord APIを使用するために必要）
```


2. メッセージを送信する先のチャンネルのIDを指定します。必要に応じて実際のチャンネルIDに置き換えてください。

```py
channel_id = xxxxxxx  # 送信先のチャンネルIDに置き換えてください
```

3. Botのアクセスを許可するトークンを指定します。Botを作成した際に取得したトークンに置き換えてください。

```py
token = 'xxxxxxx'  # Botのトークンに置き換えてください
```

4. Botがどのようなイベントを受信するかを制御するためのインテントを設定します。メッセージ内容とメッセージの受信を有効化しています。

```py
intents = discord.Intents.default()  # デフォルトのインテントを使用
intents.message_content = True  # メッセージ内容の受信を有効化
intents.messages = True  # メッセージの受信を有効化
```

ここで`True`を設定しておかなければ、メッセージの受信ができなくなります。

![](/images/sankaku7/shokika.png)
*メッセージが受信できていない様子*


5. Botのクライアントを作成し、先程設定したインテントを適用します。

```py
client = discord.Client(intents=intents)  # クライアントを作成
```

6. BotがDiscordにログインし、準備完了したときに呼び出される関数を定義します。

```py
@client.event
async def on_ready():  # Botが準備完了したときのイベント
    print('ログインしました')
```

7. Botがメッセージを受信したときに呼び出される関数を定義します。

```py
@client.event
async def on_message(message):  # メッセージを受信したときのイベント
```

8. 受信したメッセージがBot自身によるものであれば、処理をスキップします。

```py
    if message.author.bot:  # メッセージを送ったのがBot自身なら処理をスキップ
        return
```

9. 受信したメッセージが空でない場合に処理を続行します。

```py
    if message.content.strip():  # メッセージが空でない場合
```
10. 受信したメッセージを指定のチャンネルに送信します。その過程でエラーが発生した倍は例外処理をします。

```py
        try:
            channel = await client.fetch_channel(channel_id)  # チャンネルを取得
            print(f"メッセージを受信しました: {message.content}")
            print(f"チャンネル {channel.name} にメッセージを送信しようとしています: {message.content}")

            send_task = channel.send(f"{message.author.name} からのメッセージ: {message.content}")  # メッセージを送信
            print("送信タスクを開始しました")

            await send_task  # 送信タスクが完了するまで待機
            print("送信タスクが完了しました")

            print(f"メッセージをチャンネル {channel.name} に送信しました: {message.content}")

        except Exception as e:  # エラーが発生した場合
            print(f"エラー: {e}")
```

11. Botを指定のトークンでログインさせ、Discordに接続します。

```py
client.run(token)  # Botを起動
```

要するに、**このプログラムを実行すると、指定されたチャンネルにメッセージが送信された時に、同じ内容のメッセージを指定されたチャンネルに再送信するようにBotが動作します。**

-----


このプログラムを実行して、作成したBotが起動している状態でチャンネルにメッセージを送信すると、メッセージの送信者と内容をBotが表示するようになりました。
これによって、おうむ返しを実現させることができました。

![](/images/sankaku7/Echo.png)
*みんな大好き[黒棺](https://www.shonenjump.com/j/rensai/list/bleach.html)*

## さいごに
ここまで記事を読んでくださり、ありがとうございました！
今回紹介しているプログラムは本当に初歩的なものです。さまざまな記述方法によってBotをより複雑かつ豊かな機能を持ったものにすることができます。

もし興味が湧いた場合は、これをきっかけにDiscord Botを制作してみてはどうでしょうか！

**皆さんも素敵なハッピーDiscord Botライフを！！！🌸**
