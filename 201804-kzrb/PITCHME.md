# 開発環境自慢

@css[gold](Monstar Lab, Inc.)  
石倉 昇 （ @noboru-i / [@noboru_i](https://twitter.com/noboru_i)）

---

## 言い訳

開発には情報収集が大事。

ということで、  
こんな風に情報収集・記録をしていますよ、  
という紹介。

---

昔見たあの記事、どこに書いてあったっけ？ってことがよくある。  
（前日に見た記事でさえ、履歴に埋もれることはよくある）

情報は効率的にストックしておき、あとから検索できると良い。

どうせなら、JSer.infoみたいに週刊ブログ化したい。

---

考えたこと
GitHub Issueに、ページタイトル・URL・一言コメント・タグを記載する。
それを、1週間単位で1ファイルにまとめて、GitHub Pagesとして公開する。

概念図
ブラウザ -> GitHub Issue
CircleCI workflow(毎週金曜日の26:00): GitHub Issueをfetch -> markdownに整形 -> srcブランチにpush -> fetchしたissueにmilestoneを設定（作成済み管理）
srcブランチへのpushにより、CircleCI起動 -> Gatsbyによるbuildで、HTMLなどを出力 -> masterブランチにpush
masterブランチがGitHub Pagesに対応している。

---

実現するために
まずは、GitHub issue template
https://github.com/noboru-i/noboru-i.github.io/blob/src/.github/ISSUE_TEMPLATE.md
issue title: ページタイトル
issue body: URLと一言コメント
tag: あとから検索しやすいように付けておくタグ

次に、それを登録するためのbookmarklet
```
javascript:(function(){window.open('https://github.com/noboru-i/noboru-i.github.io/issues/new?title='+document.title.replace(/([\[\]])/g,'\\$1'), '_blank');})();
```

URLは自分でコピペ。（テンプレートの中に埋め込むことって出来る？）

次はCircleCI。2.0になって、定期実行ができるようになった。
https://circleci.com/docs/2.0/workflows/#scheduling-a-workflow
これを使って、毎週金曜日の26:00にnodeのスクリプトを実行する。
https://github.com/noboru-i/noboru-i.github.io/blob/src/script/fetch_issue.js
GitHub Issueをfetch -> markdownに整形 -> GitHubのsrcブランチにpush -> fetchしたissueにmilestoneを設定（作成済み管理）
をしている。

srcブランチの変更を検知して、Gatsbyによるビルド、masterブランチに成果物のpushをする。

https://www.gatsbyjs.org/
簡単にそれっぽいサイトが作れるジェネレータ。

いろんなスターター（テンプレートみたいなもの）・プラグインが公開されているので、それらを使って、改変するだけで静的なPWAアプリが作れる。
静的なので、GitHub Pagesにそのまま公開できる。

今回使ったのは https://github.com/alxshelepenok/gatsby-starter-lumen というもの。
markdownを書けば、ブログっぽく見せてくれる。
RSS/Sitemap生成、オフラインサポートなんかも標準装備。

https://github.com/gatsbyjs/gatsby/tree/master/packages/gatsby-plugin-manifest を追加すると、スマホで「ホーム画面に追加」したときにアプリっぽく見える。
これも、簡単に導入できた。

そんな感じで出来たのが、 http://noboru-i.github.io/

今後の想定
GitHubへの投稿をAndroid/iOS/ブラウザアプリ化。（どこでも、同じような手順で投稿できる）
できれば、React NativeとReact Native for Webでやりたい。（それほど複雑なコードじゃないはず。）

あとは、読んだ記事をIssueに登録していくことの習慣化。
