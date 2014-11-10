## Git勉強会

---

## いつもの作業フロー

1. git co master
2. git rebase origin/master
(git pull でも同様)
3. git co -b feature-XXXX
4. git add .
5. git commit
（4と5を複数回繰り返す）
6. git rebase -i HEAD~3
（3個のコミットをsquashする）
7. git push
8. git pull-request
（[hub](https://hub.github.com/) の機能）

---

## fast-forward の話

---

## rebase -i の話
実践してみる

---

## branch 破棄

### ローカル
### リモート

---

## GitHub flow の話
## this project flow を定義

---

## ghq

---

## gitconfig

- とは
- 私のconfigは、[GitHub](https://github.com/noboru-i/.dotfiles/blob/master/.gitconfig)参照。

---

## alias

## merge / pull

## push

---

## conflictしたときの手順
