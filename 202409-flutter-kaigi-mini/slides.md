---
theme: seriph
background: none
canvasWidth: 640
fonts:
  sans: "Noto Sans Japanese"
  serif: "Noto Serif Japanese"
  mono: "Roboto Mono"

drawings:
  persist: false
# slide transition: https://sli.dev/guide/animations.html#slide-transitions
transition: fade
# enable MDC Syntax: https://sli.dev/features/mdc
mdc: true
download: false
lineNumbers: false
---

# pigeonで<br>ネイティブ連携

FlutterKaigi mini #2 @Ishikawa

<div class="absolute right-30px bottom-30px">
@noboru-i
</div>

---
layout: two-cols
---

# About me

- name: 石倉 昇
- <carbon-logo-twitter />: noboru_i
- 所属: 株式会社モンスターラボ
- 勤務地: 富山県富山市<br>（2015年からリモート）

::right::

<div class="relative">
  <img src="/images/avatar.jpeg" class="absolute left-10 bg-gray-400 rounded-full h-40 w-40 border-4 border-secondary-400 right">
</div>

---
layout: two-cols
---

Flutterでアプリを開発してても、<br>
platform-specific codeを実行したいことが、まれによくありますよね？

<div class="text-xs text-slate-400">
※ platform-specific code<br>
= KotlinやSwiftのコード
</div>

::right::

<img src="/images/PlatformChannels.png" class="p-4">

<div class="absolute bottom-4 right-2 text-xs text-slate-400">
https://docs.flutter.dev/platform-integration/platform-channels
</div>

---

# 例えば

- 過去にネイティブで作り込んだものを流用したい
- 広告系SDKの導入
- 機器の操作系（IoTデバイスSDKとかプリンターSDKとか）
- ニッチなライブラリを利用したい（MIDI再生とか）

---

# そんなとき

### MethodChannelなどの実装をするが、手間が多い
<br>

- Dart側のinterfaceを作成
- Kotlinでメッセージ受信して処理実行して結果をまとめて返却
- Swiftで...（略）

複数の値を一度にやり取りしたい場合、それぞれのプラットフォームでdata class的なものを作成しなければならない。

---

# イメージ

全部自分で実装

<img src="/draw/before.drawio.svg" class="h-50">

---

# 例：バッテリー情報取得

ref: https://docs.flutter.dev/platform-integration/platform-channels

上記の公式ドキュメントでは、パラメータ無しで実行、数値型が返ってくるだけ。

これだけなら、ある程度簡単そうだが、
現実的には複数の値の送受信が必要となる。

今回は例として、電池残量（int）と、充電中かどうか（bool）を返す。

---

# Data class

<div class="-mt-2 grid grid-cols-2 gap-4">
<div>
Flutter (Dart)

```dart
@JsonSerializable()
class BatteryResult {
  BatteryResult({
    required this.level,
    required this.isCharging,
  });

  final int level;
  final bool isCharging;

  // fromJson, toJsonの定義
}
```

</div>
<div>
Android (Kotlin)

```kotlin
@Serializable
data class BatteryResult (
  val level: Int
  val isCharging: Boolean
)
```

iOS (Swift)

```swift
struct BatteryResult: Codable {
  var level: Int
  var isCharging: Bool
}
```
</div>
</div>

---

# Interface / Implementation (1/3)

Flutter (Dart)

invokeMethodでネイティブコードを実行した結果を、JSON文字列で受け取ってクラスに変換する。

```dart
class BatteryApi {
  static const platform = MethodChannel('samples.flutter.dev/battery');
  Future<BatteryResult> getBatteryLevel() async {
    // ex. {"level": 89, "isCharging": true}
    final resultJson = await platform.invokeMethod<String>('getBatteryLevel');
    return BatteryResult.fromJson(jsonDecode(resultJson));
  }
}
```

---

# Interface / Implementation (2/3)

Android (Kotlin)

同じchannel文字列で、同じmethod名であれば、処理して返す。

```kotlin
private val CHANNEL = "samples.flutter.dev/battery"

MethodChannel(flutterEngine.dartExecutor.binaryMessenger, CHANNEL).setMethodCallHandler {
  call, result ->
  if (call.method == "getBatteryLevel") {
    val batteryLevel = getBatteryLevel()
    val isCharging = getIsCharging()
    val json = Json.encodeToString(BatteryResult(batteryLevel, isCharging))

    result.success(json)
  }
}
```

---

# Interface / Implementation (3/3)

iOS (Swift)

```swift
let batteryChannel = FlutterMethodChannel(name: "samples.flutter.dev/battery",
                                          binaryMessenger: controller.binaryMessenger)
batteryChannel.setMethodCallHandler({
  [weak self] (call: FlutterMethodCall, result: FlutterResult) -> Void in
  if call.method == "getBatteryLevel" {
    let batteryLevel = getBatteryLevel()
    let isCharging = getIsCharging()
    let batteryResult = BatteryResult(level: batteryLevel, isCharging: isCharging)

    let encoder = JSONEncoder()
    result(encoder.encode(batteryResult))
  }
})
```

---

# 問題点

- data classを3言語で定義しなければならない
    - 追加・削除・更新したい場合に手間
    - typoする危険性
    - 型を間違える危険性
- チャンネル名と関数名がただの文字列
    - typoする危険性
    - リファクタリングしづらい

---

# そこでpigeon

https://pub.dev/packages/pigeon

> Pigeon is a code generator tool to make communication between Flutter and the host platform type-safe, easier, and faster.

Google翻訳

> Pigeon は、Flutter とホスト プラットフォーム間の通信を **タイプセーフ** 、 **簡単** 、 **高速** にするコード生成ツールです。

---

# pigeon

- GitHubの flutter/packages で管理されている安心感
https://github.com/flutter/packages/tree/main/packages/pigeon
- [video_player](https://pub.dev/packages/video_player) でも利用されている実績
- コード自動生成なので、人間より間違いが少ないはず

---

# pigeonを使う手順

1. インターフェースをDartで書く
2. `dart run pigeon`を実行（コードが自動生成される）
3. 出力されたinterfaceを、Kotlin/Swiftで実装
4. Flutter側から、出力されたDart interfaceを実行する

つまり、定義部分を書くのはDartだけで、KotlinとSwiftは実装するだけ。

---

# イメージ

これだけ不要になる。

<img src="/draw/after.drawio.svg" class="h-50">

---

# Dart interface

pigeonへの入力となるコード

```dart
class BatteryResult {
  BatteryResult({
    required this.level,
    required this.isCharging,
  });

  final int level;
  final bool isCharging;
}
@HostApi()
abstract class BatteryApi {
  BatteryResult getBatteryLevel();
}
```

---

# `dart run pigeon`実行

ファイルが出力される

```bash
dart run pigeon \
  --input pigeons/messages.dart \
  --dart_out lib/gen/messages.dart \
  --swift_out ios/Classes/messages.g.swift \
  --kotlin_out android/app/src/main/kotlin/com/example/pigeon_plugin/Messages.g.kt \
  --kotlin_package "com.example.pigeon_plugin"
```

---

# 以降、Flutterアプリプロジェクトに直接組み込む例

（個人的には、 `packages` ディレクトリを作成して、利用するライブラリごとなどにフォルダを作成し、plugin packageとすることが多いです。）


<div class="text-xs text-slate-400 mt-18">
今回のサンプルコードはこちらにあります。<br>
https://github.com/noboru-i/flutter_pigeon_sample
</div>

---

# 実装するKotlinコード

```kotlin
class MainActivity : FlutterActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        BatteryApi.setUp(
            flutterEngine.dartExecutor.binaryMessenger,
            BatteryApiImplementation(context)
        )
    }
}
class BatteryApiImplementation(private val context: Context) : BatteryApi {
    override fun getBatteryLevel(): BatteryResult {
        val level: Int = ...
        val isCharging: Boolean = ...
        return BatteryResult(level, isCharging)
    }
}
```

---

# 実装するSwiftコード

```swift
@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
    override func application(
        _ application: UIApplication,
        didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
    ) -> Bool {
        GeneratedPluginRegistrant.register(with: self)
        let controller: FlutterViewController = window?.rootViewController as! FlutterViewController
        BatteryApiSetup.setUp(binaryMessenger: controller as! FlutterBinaryMessenger, api: BatteryApiImplementation())
        return super.application(application, didFinishLaunchingWithOptions: launchOptions)
    }
}
class BatteryApiImplementation: BatteryApi {
    func getBatteryLevel() throws -> BatteryResult {
        let level = ...
        let isCharging = ...
        return BatteryResult(level: Int64(level), isCharging: isCharging)
    }
}
```

---

## こうなった

<img src="/draw/after.drawio.svg" class="h-50"> 

---

# pigeonによって

- 各言語で同じようなコードを書くという、面倒・ミスしやすい部分が、1箇所書くだけで良くなった
- 今後更新・追加が必要な場合も、Dartでの定義だけ書き換えてコマンド実行すると、必要なコードが手に入る
- （最悪、pigeonが廃れても、生成された結果コードは残っているので、頑張れば修正できる）

---
layout: center
---

# pigeonを使って、<br>ラクできるところは<br>ラクしましょう

<div class="absolute right-30px bottom-30px">
おわり
</div>
