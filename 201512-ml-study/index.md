## サーバサイド勉強会

石倉 昇 (45min)

---

このあたりの話をします。

- プログラム言語
- フレームワーク
- Database
- セキュリティ
- デプロイ
- サーバ配置場所（データセンタ、クラウド、社内…）

---

### 注意

- かなりの分量があるので、色々飛ばします。
- スライドはネットに上がるので、あとでゆっくり読んでください。
- また、単語を出しっぱなしのものもあります。  
各自ググってください。

---

## プログラム言語

---

文法と標準APIのセット。

多くの場合、実行環境もセットで決まり、  
性能面・安定性とセットで語られることも多い。

また、ライブラリの量・質で語られることも多い。  
※Rubyには10万を超えるライブラリが登録されている、など。

---

例

- Javaの標準API  
http://docs.oracle.com/javase/jp/8/api/
- PHPの標準関数リファレンス  
http://php.net/manual/ja/funcref.php

---

よく使われている言語

- Java
- PHP
- Ruby

---

### Java

- コンパイル型
- 強い静的型付け
- 記述が冗長になりがち
  - Java 8でlambda記法が出たことで、マシに
- 後方互換性が高い

---

### PHP

- スクリプト型
- エンジニアの量が多い
  - その分、初心者向けの記述が多く、検索ノイズになりやすい
  - エンジニアの質もバラバラ

---

### Ruby

- スクリプト型
- エンジニアがほかと比べて比較的少ない
- 「書いていて楽しい」が重視されて言語設計されている
- 高頻度のバージョンアップ、比較的低い後方互換性

---

### その他

- Perl
- C#
- Python
- Scala
- Kotlin
- etc...

---

## フレームワーク

---

プログラム言語に依存する。（多くの場合）

ここでは「Webアプリケーションフレームワーク」に限定する。

---

### フレームワーク導入の目的

- Webアプリを作る場合の、よくある作業を軽減するため
- ディレクトリ構成などを定義し、複数人で作業しても一貫性を保つ
  - 例：HTTPリクエストを受けるクラスはcontrollersディレクトリに置く。
- 同じフレームワークを使っているプロジェクト間での、人材の交換が比較的容易

---

余談

フレームワークの内部動作を知ることは、  
Webの仕組みを知ることにつながったり、  
綺麗なOSSであればコードの書き方を学べます。

---

大昔は、フレームワーク無しや  
独自フレームワークなんてこともありました。

今となっては、下記理由によってほとんどありません。

- メンテナンスコスト
- ドキュメント作成のコスト
- 人材の流動性

---

### フレームワークの例

- Spring (Java)
- Laravel (PHP)
- Ruby on Rails (Ruby)

---

### フレームワークの分類

フルスタックフレームワーク vs マイクロフレームワーク

※いろいろ考えるのが面倒であれば、フルスタック  
先ほど上げたのは、全てフルスタック

「フルスタックのマイクロJavaフレームワーク」  
というものもあり、単純な対義語では無さそう。

---

RubyのSinatraとかは  
マイクロフレームワークのはず。

---

## Database

---

### RDBとは

リレーショナルデータベース

ACID特性を満たす、堅牢なデータストア。

一般的に、「データベース」といえばこれ。

---

### RDBの種類

- MySQL
- PostgreSQL
- MariaDB
- Microsoft SQL Server
- Oracle Database

---

データの操作に利用する`SQL`は標準化されており、  
主要機能で出来ることに大きな違いは無い。

（ただし、方言などにより、簡単に書けるものもある。）

---

MySQLにMroongaを入れて、全文検索を行ったり、

PostgreSQLにPostGISを入れて、地理情報検索を行ったり、

など

---

性能面では大きな差が出る場合がある。  
※indexの使い方の違いなど。

---

### NoSQLとは

SQLを利用しないデータベース

- 水平分散が得意
- 構造化されていないデータも扱える

---

### NoSQLの種類

- キーバリューストア（KVS）
  - Redis, memcached
- カラム（列）指向
  - HBase, Cassandra
- ドキュメント指向
  - MongoDB, CouchDB, (DynamoDB)
- グラフ
  - Neo4j

---

それぞれ、用途・特性がぜんぜん違う。

同じKVSでも、Redisとmemcachedでは用途が違う。

---

RDBであれば、Amazon RDSなどのフルマネージドサービスがある。

処理上で楽が出来ても、運用がつらくないかよく検討した方がよい。

---

### RDB vs NoSQL

対立関係ではない。

それぞれの得意分野があるので、  
上手く組み合わせる必要がある。

ただし、複数利用すると、運用が難しくなる。  
2フェーズコミット、障害点の増加などなど

---

## セキュリティ・脆弱性

---

セキュリティに不備があると、様々な問題が発生します。

- 個人情報の流出
- 秘密鍵などの流出
- データの改竄・消失
- なりすまし

---

よくあるものを、いくつか紹介します。

詳しくは

- [安全なウェブサイトの作り方](https://www.ipa.go.jp/files/000017316.pdf)
- [体系的に学ぶ 安全なWebアプリケーションの作り方](http://www.amazon.co.jp/dp/4797361190)

---

- XSS（クロスサイトスクリプティング）
- SQLインジェクション
- CSRF（クロスサイトリクエストフォージェリ）

---

### XSS（クロスサイトスクリプティング）

ユーザが投稿した内容をJavaScriptとして実行してしまう。

---

#### 例

ブログシステムで、悪意のあるユーザが  
コメント欄に下記のようなことを書く。

```
こんにちは<script>alert('こんにちは！')</script>
```

これをそのまま解釈させてしまうと、  
画面表示時にアラートが表示されてしまう。

---

#### 対処法

データを出力する前にサニタイズする。

Railsなら`<%= %>`で出力し、`.html_safe`などはなるべく付けない。

Java(terasoluna)であれば、`f:h`を常に付ける。

---

### SQLインジェクション

ユーザの入力によって意図しないSQLが発行される。

---

#### 例

ブログシステムで、悪意のあるユーザが  
コメント欄に下記のようなことを書く。

```
こんにちは"); DROP TABLE users; #
```

これを

```
$sql = "INSERT comment values (\"$input_comment\");";
```

こんな感じで文字列として組み込むと、

```
INSERT comment values ("こんにちは"); DROP TABLE users; #");
```

テーブルの削除を実行されてしまう可能性があります。

---

#### 対処法

prepared statementを使う。

どうしても動的にSQLを組み立てる場合は、  
用意されているエスケープ関数を利用する。

---

### CSRF（クロスサイトリクエストフォージェリ）

第三者ページを経由することで、  
ユーザが意図しない操作が実行される。

---

#### 対処法

フレームワークに大抵備わっている  
csrf tokenの仕組みを正しく使う。

Railsであれば、`form_for`を適切に利用する。

---

他にもいろいろあります。

- OSコマンドインジェクション
- ディレクトリ・トラバーサル
- セッションハイジャック
- HTTPヘッダ・インジェクション
- メールヘッダ・インジェクション
- クリックジャッキング
- バッファオーバーフロー
- アクセス制御や認可制御の欠落

---

フレームワークを適切に利用することで  
防げるものが多いです。

脆弱性・フレームワークをよく学びましょう。

---

## デプロイ

---

### デプロイとは

開発したコードを、サーバなどに配置して利用できるようにすること。

---

### 昔からあるやり方

- FTP/SCPを利用したファイル転送  
- rsyncなどによるディレクトリ同期
- SVN/Gitなどによるディレクトリ同期
- デプロイツールの利用

---

### デプロイの種類

PUSH型

特定のデプロイサーバから、各サーバにコードを送る

PULL型

各サーバが、特定の場所からコードを取得する

---

### デプロイツール

ツール

- Capistrano
- Fabric

サービス

- AWS CodeDeploy

---

### 例：Capistranoの動作の一例

- デプロイ先の準備
  - ディレクトリ構造の作成など
- Gitから最新コードを取得
- 事前処理
  - ライブラリのダウンロードや、コンパイルなど
- ファイルの転送
- symlinkの切り替え
- 事後処理
  - アプリケーションサーバの再起動など

---

## サーバ配置場所

データセンタ、クラウド、社内…

---

最近では、クラウド上に配置する事が多い。

AWSでは比較的安価に、柔軟な台数変更が可能。

---

### データセンターのメリット

- 安定した設備（電源・室温など）で、  
特殊なサーバ（Fusion-ioなど）を導入出来る。
- 適切なキャパシティプランニングを行っていれば、  
クラウドより安くなることがある。

---

### 社内サーバのメリット

- 場所が許す限り、どんなものでも配置可能。
- ユーザと同一LAN内に置くことで、  
超高速・低レイテンシな通信が可能。

---

## まとめ

一言にサーバサイドと言っても、かなり幅広いです。

また、他の分野との関連もかなり多いです。

全てを極めるのは不可能なので、上手く選択と集中しましょう。

---

## 参考文献・あわせて読みたい

- [Webアプリエンジニア養成読本](http://www.amazon.co.jp/dp/4774163678)
- [体系的に学ぶ 安全なWebアプリケーションの作り方](http://www.amazon.co.jp/dp/4797361190)

---

ありがとうございました。

---

おまけ

---

## HTTPメッセージの構成を知ろう

ほとんどのやりとりはHTTPを通して行います。

- status lineはどんな構成になっているのか
- multipart/form-dataはどんな構成なのか
- Cookieはどのように設定・削除されるのか
  - フレームワークで利用するセッションとは何か

---

## ログを有効活用しよう

- デバッグしてステップ実行でも良いですが、  
環境構築に時間がかかります
- ログであれば、フレームワークで標準で付いています
  - デバッグに使ったログは、コミット前に消しましょう  
  （ルールによる）
- エラーログは、行数だけじゃなく、原因も書いてあります

---

## エラーをもみ消さない

- 開発段階で例外をもみ消すのは、大抵問題です
- 適切にエラー処理を中断し、適切に通知しましょう  
（ユーザに対しても、開発者に対しても）
- 無理やり処理を続行すると、後で想像の付かないエラーになります  
（エラーログと問題箇所が離れすぎる）

---

## 先人の知恵（？）を学ぼう

- YAGNI(You ain't gonna need it)
- DRY(Don't repeat yourself)
- QCD(Quality, Cost, Delivery)
- SOLID
  - Single Responsibility Principle
  - Open/closed principle
  - Liskov substitution principle
  - Interface segregation principle
  - Dependency inversion principle
- ボーイスカウトの規則（来た時よりも美しく）
