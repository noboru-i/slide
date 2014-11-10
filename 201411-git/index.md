## Git勉強会

---

## そもそもGitとSVNの違い

参考： https://www.atlassian.com/ja/git/tutorial/remote-repositories

SVNは中央リポジトリが1つあり、  
その作業場所としてローカルが存在します。

Gitは中央リポジトリという概念が存在せず、  
それぞれが完全なコピーを持ちます。  

---

## いつもの作業フロー

1. git fetch -p
2. git checkout master
3. git rebase origin/master
(git pull でも同様)
4. git checkout -b feature-XXXX
5. git add .
6. git commit
（5と6を複数回繰り返す）
7. git rebase -i HEAD~3
（3個のコミットをsquashする）
8. git push
9. git pull-request
（[hub](https://hub.github.com/) の機能）

---

## fast-forward の話

参考： 「各マージの解説」部分 http://d.hatena.ne.jp/sinsoku/20111025/1319497900


通常は、「マージした」という履歴を残したいので、  
 ```--no-ff``` を指定しましょう。

---

## rebase -i の話
実践してみる

1. git checkout -b test-rebase
2. touch hoge.txt
3. git add hoge.txt
4. git commit -m "tmp commit"
5. echo 'hogehoge' >> hoge.txt
6. git add hoge.txt
7. git commit -m "add text"
8. git rebase -i HEAD~2
9. git log

---

## branch 破棄（ローカル）

masterにマージ済みのブランチの場合

1. git checkout master
2. git b -d feature-hoge

---

## branch 破棄（リモート）

- GitHubの画面上で削除する
- コマンドで削除する  
git push origin :feature-hoge

---

## GitHub flow の話

定義： https://gist.github.com/Gab-km/3705015


---

## GitHub flowをベースに、<br> this project flow を定義します

<img src="img/this_project flow.png" style="background-color: #ffffff;">

---

## gitconfig とは

参考： http://git-scm.com/book/ja/v1/%E4%BD%BF%E3%81%84%E5%A7%8B%E3%82%81%E3%82%8B-%E6%9C%80%E5%88%9D%E3%81%AEGit%E3%81%AE%E6%A7%8B%E6%88%90

> これで、どのようにGitが見えて機能するかの全ての面を制御できる設定変数を取得し、設定することができます。

---

## とりあえず、私のgitconfigを~/.gitconfig に追記して下さい。

- [user]セクションは、自分のものを設定して下さい。
- 私のconfigは、[こちら](https://github.com/noboru-i/.dotfiles/blob/master/.gitconfig)。

---

## alias

- checkout と毎回入力するのが手間なので、```co```というエイリアスを作成しています
- ```l``` や ```lv``` など、複雑なコマンドを実行しているものもあります

```
[alias]
  b = branch
  ba = branch --all
  co = checkout
  d = diff
  l = log --abbrev-commit --date=iso --graph --pretty=format:'%C(red)%h %C(yellow)%d%C(green)%cd %C(blue)%cn\n%C(reset)%s\n'
  lv = log --name-status --abbrev-commit --date=iso --graph --pretty=format:'%C(red)%h %C(yellow)%d%C(green)%cd %C(blue)%cn\n%C(reset)%s\n'
  s = status --branch --short
  delete-merged-branches = !git branch --merged | grep -v \\* | xargs -I % git branch -d %```

---

## merge / pull

- mergeの```ff```は```false```になっているので、先ほどのnon-fast-forwardになっています
- pullの```ff```は```only```になっているので、fast-forwardのみを許可しています

```
[merge]
    ff = false
[pull]
    ff = only```

---

## push

参考： http://qiita.com/misopeso/items/ede49b661cc7ad30528a#2-1

- とりあえず、```current```にしておきましょう
- ```nothing``` にして、毎回コマンドで指定するのでもOKです

```
[push]
  default = current```

---

## conflictしたときの手順

シナリオ：Pull Requestを作成  
origin/masterが進んでしまった  
→auto mergeできなくなった  
表示イメージ： https://github.com/blog/843-the-merge-button

1. git fetch -p
2. git merge origin/master
3. git status # conflictしたファイルを確認する
4. conflictしたファイルを修正する
5. git add .
6. git commit
7. git push

---

## 以上です。<br>ありがとうございました。
