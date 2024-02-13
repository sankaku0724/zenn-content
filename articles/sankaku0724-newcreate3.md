---
title: "はじめてのDiscord BotをPythonで動かしてみた"
emoji: "🔥"
type: "tech"
topics:
  - "python"
  - "初心者向け"
  - "discord"
  - "discordbot"
  - "raspberrypi"
published: false
---

## はじめに
こんにちは．
今回は「**はじめてのDiscord BotをPythonで動かしてみた**」ということで記事を書いてみました．
拙い文章ではありますが，よろしくお願いします．

この記事は私がDiscord Botを初めて作成し，Pythonを用いてRaspberry Pi上で動かしてみた際の話になります．
少しでも参考になれば幸いです．


## Discord Botの作成

まずはとにかくDiscord Botを作成しなければなりません．以下のリンクからDiscord Botを作成することができます．

https://discord.com/developers/applications/

私は以下のサイトで紹介されていた手順を参考にさせていただき，Discord Botを作成してサーバーに招待しました．

https://gafuburo.net/how-to-discordbot/#toc5

ここで重要なのは，作成したBotのトークンとBotを招待したサーバーのチャンネルIDを控えておくことです．

## PythonでDiscord Botを動かしてメッセージ送信できるようにしてみよう

まず，Raspberry Pi上で以下のコマンドを実行して**discord.py**をインストールしました．このコマンドは環境によって適切なものを使用してください．

```
pip install discord.py
```

そして，zundamon.pyというファイルを作成し，以下の内容のように書き込みました．このプログラムはmsglistの中に格納されているテキストをランダムで一つ選んで送信するようになっています．

```py:zundamon
import discord
from discord.ext import commands
import random

intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix = '!', intents = intents)

msglist = [
    'あれあれ？そっぽを向いてどうしたのだー？ずんだもんの方を見られないのだー？',
    'いい子いい子してあげるのだ！',
    'ゴメンナサイゴメンナサイゴメンナサイゴメンナサイ……！',
    'これよりずんだ再教育を始めるのだ！',
    'ず、ずんだ餅がなかったら即死だったのだ……。',
    'ずんだでも食べて落ち着くのだ。',
    'ボクは誇り高きずんだの戦士なのだ！',
    'まだこれでははんごろしなのだ、今日はしっかり全ごろしにするのだ。',
    'やめるのだ！そんなことをしてもずんだは出てこないのだ！',
    '超ふぉーりんらぶなのだ！',
    '潰すのだ！',
    '天下無敵のずんだもん様を崇めるのだ！']

channel_id = xxxxxxx # 送信先のチャンネルIDに置き換えてください
token = 'xxxxxxx' # Botのトークンに置き換えてください

@bot.event
async def on_ready():
    print('起動しました.')
    choicemsg = random.choice(msglist)
    await send_message(choicemsg)
    print(choicemsg)
    await bot.close()

# プログラムを実行した際にメッセージを送信
async def send_message(text):
    channel = bot.get_channel(channel_id)
    if channel:
        try:
            await channel.send(text)
        except Exception as e:
            print(f'メッセージの送信中にエラーが発生しました: {e}')

# ボットを実行
if __name__ == '__main__':
    bot.run(token)
```

このプログラムを実行すると，作成した「ずんだもんトーク」Botが起動してチャンネルにメッセージを送信しました．

![](/images/sankaku3/zundamondis.png)


今回のプログラムではデコレーター(@bot.eventの部分)を利用しており，Discord Botが起動したときに行いたいアクション(今回の場合はon_ready関数)を定義することで自動的に呼び出されるように記述しています．
また，async def on_ready():のように関数宣言の前にasyncをつけることによって，on_ready関数をコルーチン関数として定義しています．コルーチン関数にすることで非同期処理をすることが可能になり，今回のプログラムではawaitキーワードを使用することでDiscordへのメッセージ送信を実現させました．

#### これを使えば⋯

先ほど紹介したプログラムですが，なんで「ずんだもん」？なんでmsglistにこんな文章を格納してるの？と思う方もいるかもしれません．これには理由があります．

今回，私は自身で作成したDiscord botとは別にもう一つ**VOICEVOX 読み上げbot**というDiscord botを使用しました．

VOICEVOX 読み上げbotは[KuronekoServer](https://tts.kuroneko6423.com/)様が提供しているDiscord Botです．
このDiscord BotはDiscord内のチャンネルに送信されたメッセージをボイスチャンネルで読み上げる機能を持っています．以下のサイトを参考に導入させていただきました．

https://zenn.dev/kuronekoserver/articles/1e0775307c3f1b

また，VOICEVOX 読み上げbotは好みのキャラクターボイスを選ぶことができ，私は「ずんだもん」を設定しました．
各キャラクターにはexVoiceという声優の声を直接録音した音声ファイルが存在し，特定の文章を指定することで人間が話しているような自然なセリフを再生することができます．msglistは[KuronekoServerexVoice集一覧サイト](https://kuroneko6423.com/exvoice)で紹介されていたずんだもんの文章の一部を格納したリストであり，今回のプログラムではこのリストの中からランダムでどれか一つの文章を選択して送信するといった処理をしています．

Discord内のボイスチャンネルに接続し，プログラムを実行すると，以下のようにRaspberry Piから送信した文章をDiscord内のチャンネルに表示させることができ，またそのデータの文章をVOICEVOX 読み上げbot5代目を通じて音声としても確認することができました．

![](/images/sankaku3/zundamonbot.png)

Botを組み合わせるだけで凄そうに見えます！

## さいごに
ここまで記事を読んでくださりありがとうございました！
今回紹介しているプログラムは本当に初歩的なものです．さまざまな記述方法によってBotをより複雑かつ豊かな機能を持ったものにすることができます．もし興味が湧いた場合はこれをきっかけにDiscord Botを制作してみてはどうでしょうか！

**皆さんも素敵なハッピーDiscord Botライフを！！！🌸**