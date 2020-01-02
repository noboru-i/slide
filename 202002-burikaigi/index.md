---
marp: true
size: 16:9
theme: gaia
class: invert
paginate: true
style: |
  section::after {
    content: attr(data-marpit-pagination) '/' attr(data-marpit-pagination-total);
  }
---

<style>
h1 {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
}
</style>
<style scoped>
h4 {
  font-size: 0.7em;
  text-align: right;
  margin-top: -120px;
}
</style>

# スマホアプリ開発を支えるRuby

#### Burikaigi2020 <br> 株式会社 モンスター・ラボ <br> 石倉 昇

---

## Agenda

- 自己紹介
- fastlane紹介
- fastlane + ruby
- Danger紹介
- Danger + ruby
- まとめ

---

## 自己紹介

- 名前 : 石倉 昇 (Twitter: @noboru_i)
- 出身地 : 富山県富山市
- 所在地 : 富山県富山市
- 所属 : 株式会社 モンスター・ラボ
- 勤務形態 : フルリモートワーク
- 興味範囲 : Android / Flutter / iOS / CI/CD / Rails / AWS / React / Nuxt.js / Laravel

---

## 株式会社 モンスター・ラボ

- 本社は恵比寿
- Web系の受託開発がメイン
- 音楽配信事業やゲーム開発もやってる
- 15ヵ国・26都市に拠点がある
- 恵比寿の本社内にも、外国籍メンバーが多数

---

# 本題

---

## 本題

RubyといえばRuby on Rails

ですが、それ以外の場所でもRubyの便利さ・楽しさが活用されています

今回は、スマホアプリ周辺での2つの活用事例を紹介します

---

## 想定ターゲット

- Ruby on Railsが出来て、さらに活躍の場を広げたい人
- スマホアプリのビルドに困っている人
  - 手作業が多いとか
- コードレビューに困っている人
  - 細かな指摘が多いとか

---

# 事例1 : fastlane

---

## iOSのビルド・デプロイ辛くない？

ipaを作るには、XcodeのGUIで行うか、`xcodebuild`コマンドに大量の引数を与える

テスターに確認してもらうために何度もデプロイ

---

それを解決するためにfastlane

例：アプリをビルドして、Testflightへの配信

```ruby
lane :beta do
  build_app
  upload_to_testflight
end
```

を `Fastfile` に書いて、 `fastlane beta` を実行するだけ

---

とはいえ、Schemeが複数あれば選択が必要。
Appfileなどの別ファイルに、認証情報などを記述しておく必要がある。

TODO ここは不要？後回し？

---

## fastlaneの中身

Rubyで書かれているGem

例：build_ios_app

```
module Fastlane
  module Actions
    module SharedValues
      IPA_OUTPUT_PATH ||= :IPA_OUTPUT_PATH
      DSYM_OUTPUT_PATH ||= :DSYM_OUTPUT_PATH
      XCODEBUILD_ARCHIVE ||= :XCODEBUILD_ARCHIVE # originally defined in XcodebuildAction
    end

    class BuildIosAppAction < Action
      def self.run(values)
        require 'gym'

        unless Actions.lane_context[SharedValues::SIGH_PROFILE_TYPE].to_s == "development"
          values[:export_method] ||= Actions.lane_context[SharedValues::SIGH_PROFILE_TYPE]
        end
```

---

TODO 間が必要？

---

## fastlane + Ruby

ビルドスクリプトがRubyなので、便利なことがあります

事例を3つほど紹介します

---

### ブランチ名に合わせてSchemeを変えてipaを作成

```
lane :build do
  branch_name = ENV['CIRCLE_BRANCH']
  my_scheme = case branch_name
              when "master"
                "sampleRelease"
              when /release-\d+/
                "sampleStg"
              else
                "sampleDev"
              end
  build_app(
    scheme: my_scheme
  )
end
```

---

### tag名から、出力ipaの名前を生成

```
private_lane :get_output_name do |options|
  version = get_info_plist_value(path: "./Info.plist", key: "CFBundleShortVersionString")
  date = Date.today.strftime("%Y-%m-%d")
  "#{options[:prefix]}_#{version}_#{date}"
end

lane :build do |options|
  build_app(output_name: get_output_name(options))
end
```

---

### 複数のplistのバージョンを一括で変更

```
%w{free premium}.each do |scheme|
  set_info_plist_value(
    path: "./#{scheme}.plist",
    key: "CFBundleShortVersionString",
    value: option[:new_version]
  )
end
```

---

- podのver upを通知？ https://qiita.com/tihimsm/items/055aac498c8bc3593300

---

# 事例2 : Danger

---

## Dangerとは？

https://danger.systems/ruby/

> Stop saying "you forgot to …" in code review

機械的にできるコードレビューは、機械にやらせる

---

## Danger

  ktlint / SwiftLint
  コードレビュー
  GitHub上での見え方

---

## Danger + ruby
  labelやmilestoneのチェック
  xmlが変わっていたら、スクリーンショットを求める
  対応するテストコードの変更有無
  生成物へのリンクを貼る

---

## まとめ

- Rubyが書ければ、スマホアプリをビルドできる
- Rubyが書ければ、自動コードレビュー環境が作れる

（誇張表現）
