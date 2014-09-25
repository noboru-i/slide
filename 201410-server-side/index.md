## サーバサイド・インフラ勉強会

* 平易な表現にするために、切り捨てた部分があるよ
* 私個人の意見・経験が含まれていますよ

---

## なので、<br>間違ってたら教えて下さい。

---

## 各構成要素の必要性

* ロードバランサ
* WEBサーバ
* アプリケーションサーバ
* データベース
  * RDB
  * KVS

---

### ロードバランサ

Elastic Load Balancing（ELB）とか。

* WEBサーバを複数台並べるために使うよ
  * 1台で処理しきれないリクエストを捌けるようになるよ
* WEBサーバを監視して、死んでたらリクエストを送らないように設定できるよ

---

### WEBサーバ

Apache とか Nginxとか。

* 静的なリソースを返すよ
* 動的な処理はアプリケーションサーバに依頼するよ

---

### アプリケーションサーバ

Tomcat とか Jetty とか Unicorn とか。

* JavaとかRailsのアプリケーションが動作するよ

---

### データベース（RDB）

MySQL とか PostgreSQL とか。

* 永続的な情報を保存するよ

---

### データベース（KVS）

memcache とか Redis とか。

* 主に一時的な情報を保存するよ（キャッシュですね）


<small style="margin-top: 40px;">
データベースには色んな種類があるので、広く浅く知りたい人には [7つのデータベース 7つの世界](http://shop.ohmsha.co.jp/shop/shopdetail.html?brandcode=000000003596) がオススメです。</small>

---

## ケーススタディ
  
* ケーススタディ。この案件ではこうゆう要件があったので、こうゆう構成にした。など。

---

## インフラ構築系技術の話

* Provisioning Tools
  * kickstart
* Configuration Management Tools
  * chef
  * puppet
* Orchestration Tools
  * Capistrano
  * fabric

<small style="margin-top: 40px;">参考： [Linuxサーバ構築ツールまとめ](http://tkak.hatenablog.com/entry/2012/03/11/084905)</small>

---

## Provisioning Tools

* よく知りません。

---

## Configuration Management Tools

* Nginxとかのミドルウェアのインストール・設定を行うよ。

---

## Orchestration Tools

* デプロイとかするよ。
