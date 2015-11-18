# Androidのコードを
# 自動で解析し、
# GitHubのPRに
# コメントする

---

### who are you

- 石倉 昇
- 株式会社モンスター・ラボ所属
  - 受託でアプリとかWEBサービスとか作ってます
- 富山県の自宅でリモートワーク中

<img src="img/ml.png" style="border: none">

---

### 個人活動

<small>[http://noboru-i.github.io/](http://noboru-i.github.io/)</small>
<img src="img/private_work.png" style="border: none">

---

## 本題

---

他人の書いたコードでよく見かけるこんなやつ。

TODO インデントのズレ・定数なのに小文字

---

「インデント、ズレてますよ」とか  
いちいちレビューで指摘したくない。

---

Checkstyleというものがあってだな。。。

---

Checkstyleをみんなが<strong style="color: red;">忘れず</strong>守れば解決！

---

でも、忘れますよね。  
にんげんだもの

---

一方、  
GitHubのプルリクに  
コメントあったら見ますよね？

---

Checkstyleの結果を  
コメントにしたらいいんじゃね？

---

既にありました。
<img src="img/ruby-saddler.png">

---

あとは、pushしたらこれが動けばいい

---

ということで、  
CircleCIにやってもらいましょう。

---

こんな感じ

<img src="img/auto_review_image.png" style="background-color: white; border-width: 24px;">

<small>
ついでなので、  
FindbugsとかAndroid Lintとか  
いろいろやってもらいましょう。
</small>

---

まず、CircleCIの環境設定をして、

<img src="img/circleci_env.png">

---

build.gradleにチェック項目を追加して、

```
apply from: "https://raw.githubusercontent.com/monstar-lab/gradle-android-ci-check/1.1.0/ci.gradle"
```

<small>詳細は [monstar-lab/gradle-android-ci-check](https://github.com/monstar-lab/gradle-android-ci-check)</small>

---

saddler.shとして、  
チェックの実行と  
プルリクへの反映をするscriptを書き、

```
#!/usr/bin/env bash

echo "********************"
echo "* install gems     *"
echo "********************"
gem install --no-document checkstyle_filter-git saddler saddler-reporter-github findbugs_translate_checkstyle_format android_lint_translate_checkstyle_format pmd_translate_checkstyle_format

if [ $? -ne 0 ]; then
    echo 'Failed to install gems.'
    exit 1
fi

echo "********************"
echo "* exec gradle      *"
echo "********************"
./gradlew app:check

if [ $? -ne 0 ]; then
    echo 'Failed gradle check task.'
    exit 1
fi

echo "********************"
echo "* save outputs     *"
echo "********************"

LINT_RESULT_DIR="$CIRCLE_ARTIFACTS/lint"

mkdir "$LINT_RESULT_DIR"
cp -v "app/build/reports/checkstyle/checkstyle.xml" "$LINT_RESULT_DIR/"
cp -v "app/build/reports/findbugs/findbugs.xml" "$LINT_RESULT_DIR/"
cp -v "app/build/reports/pmd/pmd.xml" "$LINT_RESULT_DIR/"
cp -v "app/build/reports/pmd/cpd.xml" "$LINT_RESULT_DIR/"
cp -v "app/build/outputs/lint-results.xml" "$LINT_RESULT_DIR/"

echo "********************"
echo "* select reporter  *"
echo "********************"

if [ -z "${CI_PULL_REQUEST}" ]; then
    # when not pull request
    REPORTER=Saddler::Reporter::Github::CommitReviewComment
else
    REPORTER=Saddler::Reporter::Github::PullRequestReviewComment
fi

echo "********************"
echo "* checkstyle       *"
echo "********************"
cat app/build/reports/checkstyle/checkstyle.xml \
    | checkstyle_filter-git diff origin/master \
    | saddler report --require saddler/reporter/github --reporter $REPORTER

echo "********************"
echo "* findbugs         *"
echo "********************"
cat app/build/reports/findbugs/findbugs.xml \
    | findbugs_translate_checkstyle_format translate \
    | checkstyle_filter-git diff origin/master \
    | saddler report --require saddler/reporter/github --reporter $REPORTER

echo "********************"
echo "* PMD              *"
echo "********************"
cat app/build/reports/pmd/pmd.xml \
    | pmd_translate_checkstyle_format translate \
    | checkstyle_filter-git diff origin/master \
    | saddler report --require saddler/reporter/github --reporter $REPORTER

echo "********************"
echo "* PMD-CPD          *"
echo "********************"
cat app/build/reports/pmd/cpd.xml \
    | pmd_translate_checkstyle_format translate --cpd-translate \
    | checkstyle_filter-git diff origin/master \
    | saddler report --require saddler/reporter/github --reporter $REPORTER

echo "********************"
echo "* android lint     *"
echo "********************"
cat app/build/outputs/lint-results.xml \
    | android_lint_translate_checkstyle_format translate \
    | checkstyle_filter-git diff origin/master \
    | saddler report --require saddler/reporter/github --reporter $REPORTER
```

---

circle.ymlのtestとして実行するように設定。

```
test:
  override:
    - ./scripts/saddler.sh
```

---

あとは、修正をpushする度に  
CircleCIがチェックしてくれる。

---

で、こうなる

<img src="img/pull_request_sample.png">

---

詳しくはWEBで。

[Androidのコードを自動で解析し、GitHubのpull requestにコメントする - Qiita](http://qiita.com/noboru_i/items/2f30296db1c8a6dfbd9b)

---

WE ARE HIRING!

[株式会社モンスター・ラボ - Wantedly](https://www.wantedly.com/companies/monstarlab)

<img src="img/wantedly.png">

---

### ありがとうございました。
