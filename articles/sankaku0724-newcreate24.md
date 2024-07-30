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

私は現在大学4年生で、卒業論文の執筆にあたり、C言語を用いたシミュレーションを行うことになりました。そして，そのプログラムを効率的に管理するため、Githubでプログラムの管理することを決意しました。本記事は、その過程で得た知見と具体的な手順をまとめたものです。

この記事が、同じく卒業研究や学術プロジェクトに取り組む学生や研究者の方々にとって、有益な情報源となれば幸いです。

### 私の動作環境

- MacOS Sonoma 14.5
- git version 2.39.3 (Apple Git-146)

## Githubとは？

[**GitHub**](https://github.co.jp/)は、プログラムのソースコードをオンラインで共有・管理するためのサービスです。これは、ソースコードのバージョン管理システムであるGitを基にしています。

## Githubで管理する




[GitHubのリポジトリ名に日本語を使用することは推奨されません。](https://webooker.info/2015/06/git-repository-name-rule/)

graduation-thesis-program

touch .gitignore

```.gitignore:.gitignore
# Ignore all text files
*.txt

# Ignore all GIF images
*.gif

# Ignore macOS system files
.DS_Store
```


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

![](/images/sankaku24/1.png)

![](/images/sankaku24/2.png)

![](/images/sankaku24/3.png)

![](/images/sankaku24/4.png)

![](/images/sankaku24/5.png)

![](/images/sankaku24/6.png)

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
