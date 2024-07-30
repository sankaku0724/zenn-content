---
title: "Githubで始める卒論プログラム管理"
emoji: "🧑‍💻"
type: "tech"
topics:
  - "github"
  - "c"
  - "git"
  - "プログラミング"
  - "初心者"
published: false
---

## はじめに

私は現在大学4年生で、卒業論文の執筆にあたり、C言語を用いたシミュレーションを行うことになりました。そして，そのプログラムをGithubで効率的に管理することにしました。本記事は、その過程で得た知見と具体的な手順をまとめたものです。

この記事が、同じく卒業研究や学術プロジェクトに取り組む学生や研究者の方々にとって、有益な情報源となれば幸いです。

### 私の動作環境

MacOS Sonoma 14.5

## Githubとは？

[**GitHub**](https://github.co.jp/)は、プログラムのソースコードをオンラインで共有・管理するためのサービスです。これは、ソースコードのバージョン管理システムであるGitを基にしたものになっています。

## Githubで管理する

### 1. Githubアカウントを作成する

以下のリンクからGithubの公式サイトに飛ぶことができます．
Githubアカウントを所持していない場合は，ここからサインアップしてアカウントを作成しましょう。
既にアカウントを持っている場合は、ログインしましょう。

https://github.com/

### 2. GitHubリポジトリを作成する

アカウントが作成できたら，ログインし、GitHubリポジトリを作成しましょう．
GitHubリポジトリとは、**ファイルやディレクトリの保存・管理場所**のことです．

画面右上のプラスアイコン（+）をクリックし、`New repository`を選択します。

![](/images/sankaku24/1.png)
*画面右上*

プラスアイコンを押すと，リポジトリの設定画面に遷移します．
まずは，リポジトリ名を決めましょう．私は「`graduation-thesis-program`」と名付けました．

また，**GitHubのリポジトリは、「Public（公開）」と「Private（非公開）」のどちらかを設定する必要があります。**
Publicにすると，誰でもアクセスできるようになり、コードを閲覧したり、フォークしたりすることが可能です。
Privateリポジトリにすると，指定されたアカウントのみがアクセスできるようになり、コードの閲覧や変更を制限することができます。

この設定は後から変更することも可能なので，とりあえず私はPrivateにしました．

![](/images/sankaku24/2.png)
*私の設定画面*

:::details リポジトリ名は日本語でつけちゃダメ！
:::message
[一般的にリポジトリ名は日本語の使用が推奨されていません。](https://webooker.info/2015/06/git-repository-name-rule/)
試しに，リポジトリ名を「卒論プログラム」にしてみると，ハイフンに置換されてしまいました．

![](/images/sankaku24/3.png)
*「卒論プログラム」と名付けてみる*

![](/images/sankaku24/4.png)
*ハイフンに置換されてしまった*
:::

他の部分は特に触らなくても大丈夫です．準備が整ったら，`Create repository`を選択しましょう。

![](/images/sankaku24/5.png)
*`Create repository`を選択*

ここまでの手順を行うことで，GitHubリポジトリを作成することができます．

![](/images/sankaku24/6.png)
*GitHubリポジトリの作成成功*

### 3. Gitにアップロードしたいものがあるディレクトリに移動する



### 4. `.gitignore`ファイルを作成する

.gitignoreファイルは、Gitで管理しないファイルやディレクトリを指定するための設定ファイルです，

touch .gitignore

```.gitignore:.gitignore
# Ignore object files
*.o

# Ignore text files
*.txt

# Ignore GIF images
*.gif

# Ignore macOS system files
.DS_Store
```

https://qiita.com/fuwamaki/items/3ed021163e50beab7154

```
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin <GitHubリポジトリのURL>
git push -u origin main
```


```
git remote add origin https://github.com/sankaku0724/graduation-thesis-program.git
```

他の人に見られません

GitHubは、変更履歴を詳細に記録することで、過去のバージョンに戻すことや、変更点のレビューが容易になります。

git reset --hard コマンドを使用して、リポジトリを指定したコミットにリセットします。
```
git reset --hard 32be7e487dfef458020b12b946e6122fb50c44cm
```

リモートリポジトリにもその変更を反映させるために、--force オプションを使用して強制的にプッシュします。
```
git push origin main --force
```


![](/images/sankaku24/7.png)

![](/images/sankaku24/8.png)

![](/images/sankaku24/9.png)

![](/images/sankaku24/10.png)

![](/images/sankaku24/11.png)

![](/images/sankaku24/12.png)

![](/images/sankaku24/13.png)

![](/images/sankaku24/14.png)






## さいごに

ここまで記事を読んでくださり、ありがとうございました！

今回の記事で紹介したGithubの使い方は，本当に最低限のものです．
他にも様々な機能やコマンドがあるので，自分に必要なものをその都度調べていくことが大事になってきます．
私もまだまだ慣れていないため，今後も実際にGithubを使いながら学んでいきたいです．

**皆さんも素敵なハッピーGithubライフを！！！🌸**
