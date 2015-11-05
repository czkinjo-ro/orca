# みんなの攻略情報 Unity WebView導入手順
* Modified: 2015-10-20
* みんなの攻略情報 SDK Version: 1.3.0

## 1. みんなの攻略情報 Unity WebView Plugin

みんなの攻略情報 Unity WebView Pluginは、  
Unity WebViewで実行可能なAndroid Unity WebView Pluginとして提供しております。

#### * **UnityのWebViewについて**
UnityでWebViewを扱うには様々な方法がありますが、  
弊社では[greeのunity-webviewプラグイン](https://github.com/gree/unity-webview)を
使って動作確認を行いました。

### 1-1. 動作環境
 * Unity 4.2.0(4.2.04f) 以降
 * Android 4.2.2 以降

### 1-2. 用語
 * **"シーン"**  
本プロジェクトでは攻略情報を表示させる各クエスト、ステージ、イベント等を「シーン」と呼称します。


## 2. ダウンロード
準備中


## 3. プロジェクトへの導入

unitypackageファイルをプロジェクトへインポートして下さい。

 [Unityプロジェクトのインポート](http://docs.unity3d.com/ja/current/Manual/HOWTO-exportpackage.html)

### 3-1. パッケージ構成
* Assets/
  * Plugins/
    * `Orca.cs`
    * Android/
      * `orca-androidsdk.jar`
      * `AndroidManifest.xml`
      * `WebViewPlugin.jar`
      * res/
    * iOS/
      * `(準備中)`

unitypackage内の「Assets/Plugins」配下のファイルを対象プロジェクトに組み込んで下さい。

### 3-2. 依存ライブラリ

貴社アプリで以下のライブラリを利用されていない場合は導入が必要となります。

|名称|必須|導入手順|
|:--|:--|:--|
|Google Play Services|任意|[情報サイト](https://developers.google.com/android/guides/setup)  （AdvertisingIdを利用しない場合は必要なし）|
|Android Asynchronous Http Client|必須|[ダウンロード](http://loopj.com/android-async-http/)「Plugins」ディレクトリ配下に設置してください。|

### 3-3. AndroidManifest.xmlの編集

Assets/Plugins/Android/AndroidManifest.xmlを参照し、以下の内容をコピーしてください。

#### * パーミッションの設定

　SDKの動作に必要な以下のパーミッションをAndroidManifest.xmlに追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

#### * アクティビティの設定

　SDKの動作に必要な以下のアクティビティをAndroidManifest.xmlに追加してください。

```xml
<activity
  android:name="net.orcaz.sdk.unity.MainActivity"
  android:configChanges="orientation|keyboardHidden|screenSize"
  android:hardwareAccelerated="true">
</activity>
<activity
  android:name="net.orcaz.sdk.review.WebDialog"
  android:configChanges="orientation|keyboardHidden|screenSize"
  android:theme="@android:style/Theme.Dialog" >
</activity>
<activity
  android:name="net.orcaz.sdk.common.DialogActivity"
  android:configChanges="keyboardHidden|orientation|screenSize"
  android:theme="@android:style/Theme.Translucent" >
</activity>
```

#### * Google Play Servicesを利用するための設定
　SDKの動作に必要な以下のメターデータをAndroidManifest.xmlに追加してください。  
　**android:valueの値に対しては適切なバージョン番号を設定してください。**

```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

### 3-4. Java Script Interfaceの定義

greeのWebViewPluginに以下のようにJavaScriptInterfaceを定義してください。

[実装例]

```Java
OrcaWebViewPlugin orcaWebViewPlugin = new OrcaWebViewPlugin();

WebView webView.addJavascriptInterface(mOrcaWebViewPlugin , OrcaWebViewPlugin.JSI_NAME);
```


## 4. SDK機能の実装

JavaScriptInterfaceに定義した関数名からSDK機能の呼び出しを行います。

### 4-1. アプリ起動時

アプリケーションの起動時にクライアントID・アプリケーションIDを設定する実装を行ってください。  
各種IDは弊社より別途（ヒアリングシート等で）ご連絡致します。
* **ConfigureはC#から呼び出す必要がありますのでご注意ください。**  

[実装例]

```c#
Orca.Configure(
  "a5af3c3f6914c2d7aeb0fb702b940a06", // クライアントID
  "0f67abb071b1a4267b4b13e5f47775df"  // アプリケーションID
);
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|クライアントID|必須|弊社で発行しご連絡致します。|
|第２引数|アプリケーションID|必須|弊社で発行しご連絡致します。|


### 4-2. 攻略情報取得

攻略情報を表示する以前のシーンが決定したタイミングで実行するよう実装を行ってください。  
シーンIDは弊社より別途（ヒアリングシート等で）ご連絡致します。
* **JavaScriptからコードを呼び出してください。**  
[実装例]

```js
OrcaUnity.getRecommendPage(
  "3ef9de9339eec72ff6ee71af1a71c156|75a3e173267a287c67c394e618775b98",  // シーンID（2個取得の場合の例）
  "レベル1",                           // ユーザレベル
  "15",                                // ユーザ経験値
  "勇者"                               // ユーザ設定キャラ
);
```

[実装例（配信制御を利用しない場合）]

```js
OrcaUnity.getRecommendPage(
  "3ef9de9339eec72ff6ee71af1a71c156",  // シーンID（1個取得の場合の例）
  "",                                  // ユーザレベル
  "",                                  // ユーザ経験値
  ""                                   // ユーザ設定キャラ
);
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|シーンID|必須|弊社で発行しご連絡致します。<br/>複数取得の場合は、" &#124; "区切りで設定（10個まで）|
|第２引数|ユーザレベル|任意|ユーザレベルで配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第３引数|ユーザ経験値|任意|ユーザ経験値で配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第４引数|ユーザ設定キャラ|任意|ユーザ設定キャラで配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|

### 4-3. 攻略情報表示チェック

攻略情報を表示する前に実行してステータスを確認してください。  
* **JavaScriptからコードを呼び出してください。**  

[実装例]

```js
var result = OrcaUnity.checkRecommendPage("3ef9de9339eec72ff6ee71af1a71c156");
if (result == 1) {
  // 攻略情報表示可能
  // 例えば、攻略情報表示ボタンを表示する処理コードを実装
} else if (result == 2) {
  // 攻略情報の表示の準備ができていない
  // 例えば、再度GetRecommendPageを呼び出すコードを実装
} else if (result == 3) {
  // 攻略情報表示不可能
}
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|シーンID|必須|弊社で発行しご連絡致します。<br/>複数シーンのチェックはできません。|

|戻り値（ステータス）|内容|
|:------|:------|
|1|攻略情報表示可能|
|2|攻略情報の表示の準備ができていない<br/>（準備した攻略情報の有効期限（デフォルト30分）が切れた等）|
|3|攻略情報表示不可能<br/>（初期化の不備や対応していないOSバージョン、予期せぬ不整合等）|

[注意点]  
* ステータスが ”2” の場合、再度「4-2.攻略情報取得」を実行する事で攻略情報を取得出来る可能性があります。  
ただし、配信制御等により表示候補がない場合は再取得を実行してもステータスは変化しません。
* ステータスが ”2” または ”3” の状態で「4-4.攻略情報表示」を実行すると攻略情報は表示されず、  
「現在メンテナンス中です。」のポップアップが表示されます。

### 4-4. 攻略情報表示

攻略情報を表示する際に実行して下さい。
* **JavaScriptからコードを呼び出してください。**  

[実装例]

```js
OrcaUnity.showRecommendPage(
  "3ef9de9339eec72ff6ee71af1a71c156" // シーンID
  0,                                 // クリア情報
  "{'OptionalData':{'puid':'1234','pname':'山田太郎','level':'レベル1', ・・・ }}" // 任意項目
);
```

[実装例（任意項目を利用しない場合）]

```js
OrcaUnity.showRecommendPage(
  "3ef9de9339eec72ff6ee71af1a71c156", // シーンID
  0,                                  // クリア情報
  ""                                  // 任意項目を使用しない
);
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|シーンID|必須|弊社で発行しご連絡致します。|
|第２引数|クリア情報|必須|ゲームクリアの状態を設定して下さい。<br/>（1:not complete 2:complete 3:before play）|
|第３引数|任意項目|任意|任意項目をJSON形式にて設定して下さい。 <br/>（設定しない場合は空文字を設定）|

【任意項目について】  
設定可能な任意項目は下記をご覧ください。  
任意項目は下記以外にも追加可能です。追加を希望する場合は、別途ご相談下さい。  
各項目は最大128桁まで設定可能です。  
任意項目にマルチバイト文字を使用する場合、スクリプトファイルの文字コードをUTF-8(BOM付き)に変更して下さい。

|任意項目名|key|設定内容|
|:------|:------|:------|
|ユーザID|puid   |ゲーム内のユーザIDを設定|
|ユーザ名|pname  |ゲーム内のユーザ名称を設定|
|対戦ボスキャラ名|bchar  |ゲーム内のボスキャラ名称を設定|
|レベル(ランク)|level  |ユーザのレベル数値または名称を設定|
|経験値|exp  |ユーザの経験値数値または名称を設定|
|設定キャラクタ名|char  |設定しているキャラクタ名を設定|
|HP|hp  |キャラクタのHP数値または名称を設定|
|スタミナ|stm  |キャラクタのスタミナ数値または名称を設定|
|攻撃力|power  |キャラクタの攻撃力数値または名称を設定|
|利用もしくは設定装備/武器関連名|item  |キャラクタの装備・武器などの名称を設定|

### 4-5. 全体的な実装例（SDK機能の実行タイミング）

1. アプリ起動
2. 「4-1.アプリ起動」各種IDの設定
3. ユーザ様のプレイ開始アクション
4. 「4-2.攻略情報取得」
5. 「4-3.攻略情報表示チェック」

6-1. チェックが "1" の場合、ユーザ様がシーン終了時に攻略情報を見るボタンを設置する等の処理  
6-2. チェックが "2" の場合、手順4に戻って再度攻略情報を取得を行う  
6-3. チェックが "3" の場合は、攻略情報がない場合の処理

7-1. ユーザ様が攻略情報を見るアクション（ボタンタップ等）  
7-2. 「4-4.攻略情報表示」  

## 5. その他API

### 5-1. クリエイティブ種別の取得
* **JavaScriptからコードを呼び出してください。**  

```js
int creativeType = Orca.getCreativeType("3ef9de9339eec72ff6ee71af1a71c156"); // シーンID
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|シーンID|必須|弊社で発行しご連絡致します。|


|戻り値（クリエイティブ種別）|内容|
|:------|:------|
|1|攻略サイトのみ|
|2|攻略動画のみ|
|3|両方|

攻略情報に動画が含まれる場合は、ゲーム内で再生しているBGMを停止することを推奨します。

## 6. ProGuardを利用する場合

ProGuard を利用してアプリケーションの難読化を行う際は F.O.X SDK のメソッドが対象とならない
よう、以下の設定 を追加してください。

```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

また、GooglePlayServiceSDKを導入されている場合、以下のページで記載されているkeep指定が記述されているかご確認ください。

[Google Play Services導入時のProguard対応](https://developer.android.com/google/play-services/setup.html#Proguard)

## 7. SDK導入後のテスト

### 7-1. 確認事項

マーケットへの申請までに、SDKを導入した状態でテストを行い、アプリケーションの動作に問題がないことを確認してください。

1. テスト用端末にて対象アプリを起動
1. **攻略情報** を呼び出す地点(シーンまたはステージ)まで画面遷移する
1. **攻略情報** を表示する
1. **攻略情報** を閉じる

弊社へテスト実行時間をお伝えください。正常にSDK機能が動作しているかログ等で確認致します。  
弊社側の確認にて問題がなければテスト完了となります。

### 7-2. デバッグモードについて

テスト時の確認等の為にデバッグモードをご用意しております。

【デバッグモードの動作】

* 配信制御は行われず、サンプルサイト及びサンプル動画が配信されます。  
* 開発環境の標準出力にログが出力されます。

テスト完了後は、弊社にてデバッグモードを解除し運用モードとなります。

----

SDK導入に関してお困りの際は、下記のメール宛先にお問い合わせください。

問い合わせ先：z-mediadev@cyber-z.co.jp
