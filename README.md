# Zenn CLI

* [📘 How to use](https://zenn.dev/zenn/articles/zenn-cli-guide)# zenn-content

## Zenn CLI の更新

Zenn CLIがzenn.devのドキュメントと異なる場合、またはCLI利用時に更新通知が表示された場合は、以下のコマンドでアップデートできる。

```
npm install zenn-cli@latest
```

## 記事の追加・変更

以下のコマンドで変更をリポジトリに反映させる。

```
git add .
git commit -m "Updated README file with new instructions"
git push origin main
```

## プレビューの確認

Zenn CLIを使ってローカルで記事のプレビューを確認できる。

```
npx zenn preview
```

このコマンドを実行後、ブラウザで[http://localhost:8000](http://localhost:8000)にアクセスしてプレビューを確認できる。
