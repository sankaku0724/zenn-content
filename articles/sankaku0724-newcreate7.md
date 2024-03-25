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

今回は「**Discord Botにおうむ返しさせる**」ということで記事を書いてみました。
拙い文章ではありますが、よろしくお願いします。

Discord Botの作成方法などについては，私が前回投稿した記事を参考にしてもらえると助かります．

https://zenn.dev/joho0724/articles/sankaku0724-newcreate3

## PythonでDiscord Botを動かしておうむ返しできるようにしてみよう

oumu.pyというPythonファイルを作成し、以下のように書き込みました。

```py:oumu.py
import discord

channel_id = xxxxxxx # 送信先のチャンネルIDに置き換えてください
token = 'xxxxxxx' # Botのトークンに置き換えてください

intents = discord.Intents.default()
intents.message_content = True
intents.messages = True

client = discord.Client(intents=intents)

@client.event
async def on_ready():
    print('ログインしました')

@client.event
async def on_message(message):
    if message.author.bot:
        return

    if message.content.strip():
        try:
            channel = await client.fetch_channel(channel_id)
            print(f"メッセージを受信しました: {message.content}")
            print(f"チャンネル {channel.name} にメッセージを送信しようとしています: {message.content}")

            send_task = channel.send(f"{message.author.name} からのメッセージ: {message.content}")
            print("送信タスクを開始しました")

            await send_task
            print("送信タスクが完了しました")

            print(f"メッセージをチャンネル {channel.name} に送信しました: {message.content}")

        except Exception as e:
            print(f"エラー: {e}")

client.run(token)
```

このプログラムを実行すると、作成したBotが起動してチャンネルにメッセージを送信しました。

![](/images/sankaku7/oumu.png)

## さいごに
ここまで記事を読んでくださりありがとうございました！
今回紹介しているプログラムは本当に初歩的なものです。さまざまな記述方法によってBotをより複雑かつ豊かな機能を持ったものにすることができます。もし興味が湧いた場合はこれをきっかけにDiscord Botを制作してみてはどうでしょうか！

**皆さんも素敵なハッピーDiscord Botライフを！！！🌸**
