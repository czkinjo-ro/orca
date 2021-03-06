# みんなの攻略情報 Android SDK導入手順

### 1-1. 動作環境
 * Android 4.2.2 以降

### 1-2. 用語

 * **"シーン"**  
本プロジェクトでは表示する攻略情報の各クエスト、ステージ、イベント等を「シーン」と呼称します。

* **"ページ"**  
貴社アプリの攻略情報を呼び出す画面の単位を「ページ」と呼称します。

## 2. SDKのダウンロード

準備中

## 3. プロジェクトへの導入

* [Eclipseプロジェクトへの導入方法](/lang/ja/doc/integration/eclipse)

## 依存ライブラリ

貴社アプリで以下のライブラリを利用されていない場合は導入が必要となります。

|名称|導入手順|
|:--|:--|
|Google Play Services|[情報サイト](https://developers.google.com/android/guides/setup)  （AdvertisingIdを利用しない場合は必要なし）|
* [Google Play Servicesの導入方法](/lang/ja/doc/google_play_services)

## AndroidManifest.xmlの編集

AndroidManifest.xmlを参照し、以下の内容をコピーしてください。

### * パーミッションの設定

　SDKの動作に必要な権限をAndroidManifest.xmlに追加します。  
　&lt;Manifest>タグ内に次のパーミッションの設定を追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

### * アクティビティの設定

```xml
<activity
  android:name="net.orcaz.sdk.Orca"
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
<activity
  android:name="net.orcaz.sdk.floating.ContentsActivity"
  android:configChanges="keyboardHidden|orientation|screenSize"
  android:hardwareAccelerated="true" >
</activity>

<service android:name="net.orcaz.sdk.floating.FloatService" />
```

### * 画面回転の設定

Androidアプリの場合、画面の回転の設定については`AndroidManifest.xml`の`android:configChanges`を編集してください。

### * Google Play Servicesを利用するための設定
　SDKの動作に必要な以下のメターデータをAndroidManifest.xmlに追加してください。  
　**android:valueの値に対しては適切なバージョン番号を設定してください。**

```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

## ProGuardを利用する場合

ProGuard を利用してアプリケーションの難読化を行う際は みんなの攻略情報SDK のメソッドが対象とならない
よう、以下の設定を追加してください。

```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

また、GooglePlayServiceSDKを導入されている場合、以下のページで記載されているkeep指定が記述されているかご確認ください。

[Google Play Services導入時のProguard対応](https://developer.android.com/google/play-services/setup.html#Proguard)


## 4. SDK機能の実装

### 4-1. アプリ起動時

**アプリケーションの起動時** にクライアントID・アプリケーションIDを設定する実装を行ってください。  
各種IDは弊社より別途（ヒアリングシート等で）ご連絡致します。

[実装例]

```java

import net.orcaz.sdk.Orca;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    Orca.configure(
      this,                               // アクティビティ
      "86b8094d44c175850b18ec31eb9adf71", // クライアントID
      "d1d1b00e741d2417a4a1f55b1eda5bed"  // アプリケーションID
    );
}
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|アクティビティ|必須|アプリケーションのアクティビティを設定してください。<br/>|
|第２引数|クライアントID|必須|弊社で発行しご連絡致します。<br/>|
|第３引数|アプリケーションID|必須|弊社で発行しご連絡致します。<br/>※ iOSとAndroid共通で利用可能|

### 4-2. アプリ起動時(インストール後初回起動時)

アプリケーションをインストール後、**初回起動時** のみ実行するよう実装を行ってください。  

[実装例]

```java

import net.orcaz.sdk.Orca;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    Orca.configureFirstStart(
      this,                               // アクティビティ
      "86b8094d44c175850b18ec31eb9adf71", // クライアントID
      "d1d1b00e741d2417a4a1f55b1eda5bed"  // アプリケーションID
    );
}
```

※パラメータは「4-1. アプリ起動時」と共通。

### 4-3. 攻略情報フロートアイコン表示要求

貴社アプリ画面上に**攻略情報フロートアイコン**を表示したいタイミングで実行するよう実装を行ってください。  
ページID、シーンIDは弊社より別途（ヒアリングシート等で）ご連絡致します。  

[実装例]

```java
Orca.showFloatIcon(
  this,                               // アクティビティ
  "d4dc9737ecb40d25d1bc5b8a1f7798d4", // ページID
  "65928b3ceeb3e9cb24d917e5532ad332|afc4a607d40edbe7c51853ac3af0910f",  // シーンID（攻略情報が複数ある場合の例）
  "レベル1",                           // ユーザレベル
  "15",                                // ユーザ経験値
  "勇者"                               // ユーザ設定キャラ
  "{'OptionalData':{'puid':'1234','pname':'山田太郎','level':'レベル1', ・・・ }}" // 任意項目
);
```

[実装例（配信制御を利用しない場合）]

```java
Orca.showFloatIcon(
  this,                                // アクティビティ
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",  // ページID
  "65928b3ceeb3e9cb24d917e5532ad332",  // シーンID（攻略情報が1つだけの場合の例）
  "",                                  // ユーザレベル
  "",                                  // ユーザ経験値
  "",                                  // ユーザ設定キャラ
  ""                                   // 任意項目
);
```

#### 攻略情報フロートアイコンの表示を切り替える

貴社アプリの画面切り替え等のタイミングで、呼び出し地点のページID・シーンIDを指定し
「4-3. 攻略情報フロートアイコン表示要求」を実行してください。
既に表示中の攻略情報フロートアイコンを破棄し、新たにフロートアイコンを表示します。

#### 攻略情報フロートアイコンのデザインを設定する

ヒアリングシート等で、各ページでご使用するアイコンデザイン、サイズ、画像をお伺いの上、配信システムに登録致します。

#### 攻略情報フロートアイコンを非表示にする

一度呼び出した攻略情報フロートアイコンを非表示にするには、シーンIDを指定しない状態で
「4-3. 攻略情報フロートアイコン表示要求」を実行してください。


[実装例]

```java
Orca.showFloatIcon(
  this,                                // アクティビティ
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",  // ページID
  "",                                  // シーンID（指定なし）
  "",                                  // ユーザレベル
  "",                                  // ユーザ経験値
  "",                                  // ユーザ設定キャラ
  ""                                   // 任意項目
);
```


|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|アクティビティ|必須|アプリケーションのアクティビティを設定してください。<br/>|
|第２引数|ページID|必須|弊社で発行しご連絡致します。<br/>※ iOSとAndroid共通で利用可能|
|第３引数|シーンID|任意|弊社で発行しご連絡致します。<br/>複数取得の場合は、" &#124; "区切りで設定（20個まで）<br/>指定なしの場合、表示中の攻略情報フロートアイコンを非表示にします。<br/>※ iOSとAndroid共通で利用可能|
|第４引数|ユーザレベル|任意|ユーザレベルで配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第５引数|ユーザ経験値|任意|ユーザ経験値で配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第６引数|ユーザ設定キャラ|任意|ユーザ設定キャラで配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第７引数|任意項目|任意|任意項目をJSON形式にて設定して下さい。 <br/>（設定しない場合は空文字を設定）|

【任意項目について】  
設定可能な任意項目は下記をご覧ください。  
任意項目は下記以外にも追加可能です。追加を希望する場合は、別途ご相談下さい。  
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

※各項目は最大128桁まで設定可能です。  


### 4-4. アプリ離脱時と復帰時

アプリ離脱時と復帰時に、攻略情報フロートアイコン表示／非表示を制御します。

離脱時、復帰時にSDKを呼び出す実装を行ってください。

[実装例]

```java
@Override
public void onResume() {
    Orca.resume();
}

@Override
public void onPause() {
    Orca.pause();
}
```

## 5. SDK導入後のテスト

### 5-1. 確認事項

マーケットへの申請までに、SDKを導入した状態でテストを行い、アプリケーションの動作に問題がないことを確認してください。

1. テスト用端末にて対象アプリを起動
1. **攻略情報** を呼び出す地点(シーンまたはステージ)まで画面遷移する
1. **フロートアイコン** を表示する
1. **フロートアイコン** をタップして攻略情報を表示する
1. **攻略情報** を閉じる

弊社へテスト実行時間をお伝えください。正常にSDK機能が動作しているかログ等で確認致します。  
弊社側の確認にて問題がなければテスト完了となります。

### 5-2. デバッグモードについて

テスト時の確認等の為にデバッグモードをご用意しております。

【デバッグモードの動作】

* 配信制御は行われず、サンプルサイト及びサンプル動画が配信されます。  
* 開発環境の標準出力にログが出力されます。

|デバッグモードシーン|シーンID|
|:------|:------|
|シーン1|fc7f24abc577f37d44b6fb0095dcd976|
|シーン2|aee4857751950eee5441057d436e506a|
|シーン3|ea0a160b2a496c33efe0c9511f61b34f|
|シーン4|c0f7d3256b91dc80faab2d0ca34696e6|
|シーン5|a3c9cc0625a56a2f9abb1400f2e17f80|
|シーン6|46d8ca247c7cdd3f81c0649b1bc3c2fc|
|シーン7|f576372b68c175cb52730f05a510eb86|
|シーン8|3ae0fa5850a7ef180b56617f271124a7|
|シーン9|a353d25ddc95549b6bb848fbf6401510|
|シーン10|a63b500d519653e2306c77213ef91eb9|

テスト完了後は、弊社にてデバッグモードを解除し運用モードとなります。

----

SDK導入に関してお困りの際は、下記のメール宛先にお問い合わせください。

問い合わせ先：z-mediadev@cyber-z.co.jp
