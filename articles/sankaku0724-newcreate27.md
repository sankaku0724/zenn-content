---
title: "Formspreeでコンタクトフォームを実装する"
emoji: "📧"
type: "tech"
topics:
  - "github"
  - "githubpages"
  - "web"
  - "mail"
  - "html"
published: false
---

## はじめに

近年、Github Pagesでホームページを作成する人は少なくないでしょう。

Github Pagesは静的ウェブサイトのホスティングに最適ですが、サーバーサイドの処理ができないため、コンタクトフォームの実装が難しいと思われがちです。
しかし、**Formspree**を利用することで、簡単にコンタクトフォームを実装することができます。

## Formspreeってなんやねん

[**Formspree**](https://formspree.io/)は、静的ウェブサイトにフォーム機能を追加するためのサービスです。HTMLフォームを作成し、Formspreeが提供するエンドポイントにデータを送信するだけで、メールでフォームの内容を受け取ることができます。

Formspreeには以下のリンクからアクセスできます。

https://formspree.io/

ちなみに、無料プランでは月に50通までのフォーム送信が可能になっています。
お財布にも優しい！

## Formspreeを利用してコンタクトフォームを実装する

ここからは、Formspreeを利用してコンタクトフォームを実装するための具体的な手順について紹介します。

### Formspreeの登録

まず、Formspreeのアカウントを作成する必要があります。

![](/images/sankaku27/1.png)
*「Sign in」をクリック*

必要事項を入力しましょう。

![](/images/sankaku27/2.png)

登録を終えたら「+ Add New」をクリックし、「New Form」を選択しましょう。

![](/images/sankaku27/3.png)

すると、フォームの作成のためにフォーム名などの入力が求められます。
**ここに入力したメールアドレスで、コンタクトフォームから送られてきたお問い合わせを受信することができます。**

![](/images/sankaku27/4.png)

フォームを作成すると、FormspreeからフォームIDが提供されます。
メモしておきましょう。

![](/images/sankaku27/5.png)

### ホームページとFormspreeを結びつける

htmlファイル内に以下のように記述してみます。

:::message
ここで、action属性の値をFormspreeから提供されたフォームIDに置き換える必要があります。
:::

```html
<h2>お問い合わせ</h2>
<form action="https://formspree.io/f/提供されたフォームID" method="POST">
  <label for="name">お名前:</label><br>
  <input type="text" id="name" name="name" required><br>
  
  <label for="email">メールアドレス:</label><br>
  <input type="email" id="email" name="email" required><br>
  
  <label for="message">お問い合わせ内容:</label><br>
  <textarea id="message" name="message" required></textarea><br>
  
  <button type="submit">送信</button>
</form>
```

すると、以下の画像のような画面に仕上がりました。

![](/images/sankaku27/6.png)
*コンタクトフォーム*

実際にお問い合わせを送信してみます。

![](/images/sankaku27/8.png)
*適当な情報を入力して送信*

送信してみると、以下のような画面になり、「サイトに戻る」を押すとコンタクトフォームの画面に戻ることができました。

![](/images/sankaku27/9.png)

Formspreeのフォーム作成時に指定したメールアドレスの受信ボックスを確認してみます。
すると、**コンタクトフォームに入力した情報をしっかり受信できていました！**

![](/images/sankaku27/10.png)
*受信できている*

このように、Formspreeを利用することで簡単にコンタクトフォームを実装することができます！

#### Formspreeをホームページに取り入れてみよう！

この記事ではhtmlファイルだけで完結させていますが、cssファイルを用いてデザインを整えることももちろん可能です。
私はGithub Pagesで作成した[自分のホームページ](https://sankaku0724.github.io/)でFormspreeを利用しています。

![](/images/sankaku27/7.png)
*2024年8月現在の私のホームページのコンタクトフォーム*

Formspreeって便利ですね〜！

## さいごに

ここまで記事を読んでくださり、ありがとうございました！

Formspreeは便利なサービスです。プログラミングの知識がない方でもHTMLの基本的な構造を理解していれば、数分でフォームを作成し、運用を開始できます。また、送信されたデータを安全に処理し、指定したメールアドレスに転送してくれるため、セキュリティ面でも安心です。

![](/images/sankaku27/11.png)
*無料でreCAPTCHA機能を使うこともできます！*

興味を持った方は、ぜひ一度Formspreeを使ってみてください！

**皆さんも素敵なハッピーホームページライフを！！！🌸**
