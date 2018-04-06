## 情報収集・管理のために
## 作ったもの

<p style="font-size: 0.6em; text-align: right;">
Monstar Lab, Inc.  
石倉 昇 （ @noboru-i / [@noboru_i](https://twitter.com/noboru_i)）
</p>

---

## 言い訳

開発には情報収集・管理が大事。

ということで、  
私の情報収集・管理環境の紹介。

（実は始めたばっかり）

---

<p style="font-size: 0.7em;">
昔見たあの記事、どこに書いてあったっけ？ってことがよくある。  
（前日に見た記事でさえ、履歴に埋もれることはよくある）
</p>

<p style="font-size: 0.7em;">
情報は効率的にストックしておき、あとから検索できると良い。
</p>

<p style="font-size: 0.7em;">
どうせなら、JSer.infoみたいに週刊ブログ化したい。
</p>

---

## 考えたこと

GitHub Issueに以下の情報を記載する。

- ページタイトル
- URL
- 一言コメント
- タグ

それを、1週間単位で1ファイルにまとめて、GitHub PagesとしてBlog的に公開する。

---?image=./201804-kzrb/flow.png

<!--
@startuml
私 -> "GitHub Issue": ブラウザの\nBookmarkletで登録

... 毎週金曜日の26:00JSTに定期実行 ...

CircleCI -> "GitHub Issue": fetch
CircleCI <-- "GitHub Issue" : 未掲載のIssue

CircleCI -> CircleCI: Markdownに整形
CircleCI -> GitHub: srcブランチにpush
CircleCI -> "GitHub Issue": fetchしたものに\nmilestoneを設定

... srcブランチの変更を検知 ...

CircleCI -> CircleCI: Gatsbyによるbuild
CircleCI -> GitHub: masterブランチにpush

...  ...

私 -> "GitHub Pages"
"GitHub Pages" -> GitHub: masterブランチの\nHTMLを取得
私 <-- GitHub: PWAサイトが見れる！

@enduml
-->

---

## 実現するために

まずは、GitHub issue template

[ISSUE_TEMPLATE.md](https://github.com/noboru-i/noboru-i.github.io/blob/src/.github/ISSUE_TEMPLATE.md)

```
## URL

## Content
```

- issue title: ページタイトル
- issue body: URLと一言コメント
- tag: あとから検索しやすいように付けておくタグ

これを作っておくと、フォーマットを強制できる。

---

次に、それを登録するためのbookmarklet  
（実際は1行）

```js
javascript:(
  function(){
    window.open(
      'https://github.com/noboru-i/noboru-i.github.io/issues/new?title='
      +document.title.replace(/([\[\]])/g,'\\$1'),
      '_blank');
  }
)();
```

URLは自分でコピペ。  
（テンプレートの中に埋め込むことって出来る？）

---?image=./201804-kzrb/flow.png

---

次はCircleCI。

2.0になって、定期実行ができるようになった。  
https://circleci.com/docs/2.0/workflows/#scheduling-a-workflow

これを使って、毎週金曜日の26:00にNodeのスクリプトを実行する。


---?image=./201804-kzrb/flow.png

---

srcブランチを変更してるので、次のビルドが実行される。

---?image=./201804-kzrb/flow.png

---

## Gatsby?

https://www.gatsbyjs.org/  
簡単にそれっぽいサイトが作れるジェネレータ。

いろんなStarter・Pluginが公開されているので、それらを使って、改変するだけでそれっぽい静的なPWAアプリが作れる。  
静的なので、GitHub Pagesにそのまま公開できる。

---

今回使ったのは [alxshelepenok/gatsby-starter-lumen](https://github.com/alxshelepenok/gatsby-starter-lumen) というもの。

- markdownを書けば、ブログっぽくなる。
- RSS/Sitemap生成、offline対応も標準装備。
- `gatsby-plugin-manifest` を追加すると、スマホで「ホーム画面に追加」したときにアプリっぽく見える。

---

そんな感じで出来たのが、  
http://noboru-i.github.io/

---

## 今後の想定

GitHubへの投稿をAndroid/iOS/ブラウザアプリ化。  
（どこでも、同じような手順で投稿できる）

できれば、React NativeとReact Native for Webでやりたい。  
（それほど複雑なコードじゃないはず。）

あとは、読んだ記事をIssueに登録していくことの習慣化。
