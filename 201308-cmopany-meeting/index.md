## サービス開発を<br>自動化・効率化

<small>Created by Noboru Ishikura / @noboru_i</small>

---

世の中には、便利なツールがいろいろあります

---

今回、10個程度のツールを矢継ぎ早に紹介します

---

1つでも、  
調べてみよう、使ってみよう  
と思っていただければ幸いです

---

### 簡単に捨てられる環境が欲しい

<small>新しいバージョンのソフトを試したくても、Macの環境壊したくないですよね・・・</small>

---

## Vagrant

<img src="image/logo_vagrant.png" width="25%">

[http://www.vagrantup.com/](http://www.vagrantup.com/)

---

## Ubuntuの仮想マシンを
## 立ち上げる

    $ vagrant box add base http://files.vagrantup.com/lucid32.box
    $ vagrant init
    $ vagrant up
    $ vagrant ssh

これだけで、まっさらなUbuntu環境が使える

---

## いらなくなったら

    $ vagrant destroy

---

<!-- Chef -->
### 開発・動作環境を
### チーム全員で揃えたい

<small>けど、手順書とか、作るの面倒ですよね・・・</small>

---

## Chef

<img src="image/logo_chef.png">

[http://www.opscode.com/chef/](http://www.opscode.com/chef/)

---

Vagrantと連携して、  
仮想マシンの立ち上げ  
ミドルの設定まで一気に出来る

* apache, MySQLのインストール・起動
* httpd.confの設定
* DBユーザの作成

---

Berkshelfも利用すると、  
誰かが検証済みの構成を持ってこれる

[http://berkshelf.com/](http://berkshelf.com/)

<small>前提条件があったりして、使えないこともありますが。。。</small>

---

1回作っておけば、  
同じプロジェクトに後から入ってくる人はもちろん、  
同じようなインフラ構成のプロジェクトで流用できる

---

<!-- CocoaPods -->
### iOSのライブラリを管理したい

<small>1回入れるのはいいですけど、bugfixされても、バージョンアップがめんどうですよね・・・</small>

---

## CocoaPods

<img src="image/logo_cocoapods.png">

[http://cocoapods.org/](http://cocoapods.org/)

---

JSONKitは1.4以降、Reachabilityは3.0系の最新を使いたい

    $ vim Podfile
      platform :ios, '5.0'
      pod 'JSONKit',      '~> 1.4'
      pod 'Reachability', '~> 3.0.0'
    $ pod install

---

* バージョン指定
* gitのcommit指定

など、いろいろできる

---

<!-- Gradle -->
### Androidのビルドを簡単にやりたい

<small>「広告なしの課金版」と「無料版」の両方更新とかめんどうですよね・・・</small>

---

## Gradle

<img src="image/logo_gradle.gif">

[http://www.gradle.org/](http://www.gradle.org/)

---

## Build Type

本番用と開発用で設定が別々のとき

<small>開発時は開発用サーバに接続するとか。</small>

---

## Product flavors

無料版と課金版で機能が変わるとき

<small>金を払ってもらった人には、広告を出さないとか。</small>

---

## ライブラリの管理

mavenリポジトリから取ってこれる

---

<!-- Sass -->
### CSSを綺麗に書きたい

<small>CSSって、場当たり的に書いてると大変なことになりますよね・・・</small>

---

## Sass

<img src="image/logo_sass.gif" />

[http://sass-lang.com/](http://sass-lang.com/)

---

### 出来ること

* 変数の定義
* セレクタのネスト
* 継承
* などなど。。。

---


	$vmargin: 2em;
	table.hl {
		margin: $vmargin 0;
		td.ln {
			text-align: right;
		}
	}

が

	table.hl {
		margin: 2em 0;
	}
	table.hl td.ln {
		text-align: right;
	}

に変換されます

<small>SCSS(Sassy CSS)という記法です。SASS記法もあります。</small>

---

<!-- Compass -->
### CSSがいろいろめんどくさい

<small>ベンダープレフィックスとか、CSS Spriteとか・・・</small>

---

## Compass

<img src="image/logo_compass.png"/>

[http://compass-style.org/](http://compass-style.org/)

---

## ベンダープレフィックス

	.simple  {
		@include border-radius(4px, 4px);
	}

と書いておけば、

	.simple {
		-webkit-border-radius: 4px 4px;
		-moz-border-radius: 4px / 4px;
		-khtml-border-radius: 4px / 4px;
		border-radius: 4px / 4px; 
	}

になる

<small>1コつけ忘れて、古いFirefoxで見た時だけ崩れる、とかがなくなる。はず。</small>

---

## CSS Sprite

* images/my-icons/new.png
* images/my-icons/edit.png
* images/my-icons/save.png
* images/my-icons/delete.png

として、32pxの画像を4つ置き、

	@import "my-icons/*.png";
	@include all-my-icons-sprites;

と書くと、

---


	.my-icons-sprite, .my-icons-delete, .my-icons-edit, .my-icons-new,
	.my-icons-save { background: url('/images/my-icons-s34fe0604ab.png') no-repeat; }

	.my-icons-delete { background-position: 0 0; }
	.my-icons-edit { background-position: 0 -32px; }
	.my-icons-new { background-position: 0 -64px; }
	.my-icons-save { background-position: 0 -96px; }

となる

<small>sprite画像作っけど、内1個使わなくてよかったー → フォルダから削除</small>

---

<!-- Grunt -->
### JavaScript, CSSの管理がめんどう

concat（連結）して、minify（圧縮）して・・・

---

## Grunt

<img src="image/logo_grunt.png" alt="" />

[http://gruntjs.com/](http://gruntjs.com/)

---

concat（連結）

Gruntfile.js に下記を記載

	grunt.initConfig({
	  concat: {
	    options: {
	      separator: ';',
	    },
	    dist: {
	      src: ['src/intro.js', 'src/project.js', 'src/outro.js'],
	      dest: 'dist/built.js',
	    },
	  },
	});

で

	$ grunt concat:dist

と実行すると3つのファイルが結合されてbuilt.jsが出来ます

---

minify（圧縮）

Gruntfile.js に下記を記載

	grunt.initConfig({
	  uglify: {
	    my_target: {
	      files: {
	        'dist/built.min.js': ['dist/built.js']
	      }
	    }
	  }
	});

で

	$ grunt uglify:my_target

と実行すると、built.jsを圧縮したbuilt.min.jsが出来ます

---

2つを合わせて、grunt.initConfigに下記を追加

    grunt.registerTask('default', ['concat', 'uglify']);

で

    $ grunt

とすると、concatしてminifyされたjsファイルが出来ます

---

紹介した、concat、uglify以外にも

* grunt-contrib-compass
* grunt-contrib-watch
* grunt-contrib-jshint
* grunt-contrib-coffee
* grunt-contrib-imagemin

などなど

---

### JavaScript, CSSライブラリの管理がめんどう

<small>jQueryのbugfixがリリースされたらしいけど、<br>
取り込むのめんどい・・・</small>

---

## Bower

<img src="image/logo_bower.png" width="40%" />

<a href="http://bower.io/">http://bower.io/</a>

---

Backbone.js, jQueryの最新版を利用したい場合

    $ vim bower.json
    {
      略
      "dependencies": {
        "underscore": "latest",
        "backbone": "latest",
        "jquery": "latest"
      },
    }
    $ bower install

これで、bower_components フォルダに<br>各ライブラリとその依存関係が配置される

---

<!-- Capistrano -->
## デプロイを自動化したい

<small>
FTPで転送して、<br>
sshでログインして、<br>
解凍して、、、<br>
とか、めんどい・・・
</small>

---

## capistrano

<img src="image/logo_capistrano.png" alt="" />

[http://www.capistranorb.com/](http://www.capistranorb.com/)

---

## Fabric

[http://fabfile.org/](http://fabfile.org/)

---

詳しくは、よく知らないので、  
むしろ教えて下さい m(__)m

---

<!-- Jenkins -->
### デプロイまでの工程がめんどう

<small>commitしてから、静的解析して、単体テスト流して、結合テスト流して、デプロイして・・・</small>

---

## Jenkins

<img src="image/logo_jenkins.png" alt="" />

[http://jenkins-ci.org/](http://jenkins-ci.org/)

---

## GitのPUSHをトリガにする

Gitの変更を監視する or GitHub/GitLabから通知する

---

## ビルド・パイプライン

プロセスを分割して登録しておけば、  
それぞれのプロセスが成功した場合に、  
次のプロセスが実行される

---

<!-- まとめ -->
簡単にですが、いろんなツールを紹介しました。

---

自分も、詳しくないものが多いので、  
一緒に勉強して行きましょう

---

そして、ラクしましょう

---

以上です

