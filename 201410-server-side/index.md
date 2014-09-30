## サーバサイド・インフラ勉強会

* 平易な表現にするために、切り捨てた部分があるよ
* 私個人の意見・経験が含まれていますよ

---

## なので、<br>間違ってたら教えて下さい。

---

## 各構成要素の必要性

<img src="img/network_diagram.png" style="background-color: #ffffff;">

---

* CDN
* ロードバランサ
* WEBサーバ
* アプリケーションサーバ
* データベース
  * RDB
  * NoSQL

---

### Contents Delivery Network(CDN)

Amazon CloudFront とか Akamai とか。

* （主に静的な）リソースをキャッシュするよ
* 地理的に分散されているため、どこからでも高速にアクセス出来るよ

---

### ロードバランサ

Elastic Load Balancing（ELB）とか。

* WEBサーバを複数台並べるために使うよ
  * 1台で処理しきれないリクエストを捌けるようになるよ
* WEBサーバを監視して、死んでたらリクエストを送らないように設定できるよ

---

### WEBサーバ

Apache とか Nginxとか。

* 静的なリソース(HTML/CSS/JSなど)を返すよ
* 動的な処理はアプリケーションサーバに依頼(Reverse proxy)するよ

---

### アプリケーションサーバ

Tomcat とか Jetty とか Unicorn とか。

* JavaとかRailsのアプリケーションが動作するよ
* 動的なリソースを返すよ
  * DBの情報をJSON形式にして返したり
  * ログインユーザに合わせたHTMLを返したり

<small style="margin-top: 40px;">
物理的には、WEB＋アプリケーションで1台のサーバにすることが多いと思います。
</small>

---

### データベース（RDB）

MySQL とか PostgreSQL とか。

* 永続的な情報を保存するよ
* データ同士に関連性を持たせられるよ
* 読み込みはレプリカを増やせばスケールできるけど、書き込みがネックになると辛いよ

---

### データベース（NoSQL）

memcache とか Redis とか MongoDB とか。

* RDB以外をひとまとめに呼んでるだけなので、  
  いろいろな種類があるよ
  * memcacheは、一時的な情報を保存するのによく使われるよ
  * Redisは、memcacheに近い使い方も出来るし、  
  もうちょっと凝ったことも出来るよ
  * MongoDBは、巨大なデータを扱うときによく使われるよ


<small style="margin-top: 40px;">
データベースには色んな種類があるので、広く浅く知りたい人には [7つのデータベース 7つの世界](http://shop.ohmsha.co.jp/shop/shopdetail.html?brandcode=000000003596) がオススメ。</small>

---

## ケーススタディ
  
* ケーススタディ。この案件ではこうゆう要件があったので、こうゆう構成にした。など。

---

## Infrastracture as a codeの話

* Provisioning Tools
  * kickstart
* Configuration Management Tools
  * chef
  * puppet
* Orchestration Tools
  * Capistrano
  * fabric

<small style="margin-top: 40px;">参考： <br>
[Linuxサーバ構築ツールまとめ](http://tkak.hatenablog.com/entry/2012/03/11/084905)<br>
[最近のインフラ系ツールが多すぎて何が何だかわからない！からの卒業](http://qiita.com/kasaharu/items/59b8d3a76b4a22eab88e)</small>

---

## Provisioning Tools

* よく知りません。

---

## Configuration Management Tools

* Nginxとかのミドルウェアのインストール・設定を行うよ。

---

## Orchestration Tools

* デプロイとかするよ。
