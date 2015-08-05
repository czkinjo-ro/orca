#ORCA Unity導入手順
* Modified: 2016-07-24
* Unity Plug-in Version: 1.0.0
* Android SDK Version: 1.0.0

##1.ORCA Unity Plugin

ORCA Unity Pluginは、C#から実行可能なAndroid Unity Pluginとして提供しております。

####1-1.動作環境
 * Unity 4.2.0(4.2.04f) 以降
 * Android 4.2.2 以降

####1-2.用語
 * **"シーン"**
本プロジェクトでは攻略情報を表示させる各クエスト、ステージ、イベント等を「シーン」と呼称します。

##2.ダウンロード

準備中

##3.インストール

unitypackageファイルをプロジェクトへインポートして下さい。

 [Unityプロジェクトのインポート](http://docs.unity3d.com/ja/current/Manual/HOWTO-exportpackage.html)

####3-1.パッケージ構成
* Assets/
  * Plugins/
    * `Orca.cs`
    * Android/
      * `orca-androidsdk.jar`
      * `AndroidManifest.xml`
      * res/
    * iOS/
      * `(対応未定)`

unitypackage内の「Assets/Plugins」配下のファイルを対象プロジェクトに組み込んで下さい。

####3-2.依存ライブラリ

貴社アプリで以下のライブラリを利用されていない場合は導入が必要となります。

|名称|導入手順|
|:--|:--|
|Google Play Services|Setting Up Google Play Services | Android Developers](https://developers.google.com/android/guides/setup)  ※AdvertisingIDを利用しない場合は必要ありません。|
|Android Asynchronous Http Client|[ダウンロード](http://loopj.com/android-async-http/)「Plugins」ディレクトリ配下に設置してください。|
|google-gson|[ダウンロード](http://search.maven.org/#artifactdetails%7Ccom.google.code.gson%7Cgson%7C2.3.1%7Cjar)「Plugins」ディレクトリ配下に設置してください。|
####3-3.AndroidManifest.xmlの編集

Assets/Plugins/Android/AndroidManifest.xmlを参照し、以下の内容をコピーしてください。

#####*パーミッションの設定

　SDKの動作に必要な権限をAndroidManifest.xmlに追加します。  
　<Manifest>タグ内に次のパーミッションの設定を追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

#####*アクティビティの設定

```xml

<activity
  android:name="jp.co.cyberz.orcaz.sdk.unity.MainActivity"
  android:configChanges="orientation|keyboardHidden|screenSize"
  android:hardwareAccelerated="true">
</activity>
<activity
  android:name="jp.co.cyberz.orcaz.sdk.review.WebDialog"
  android:configChanges="orientation|keyboardHidden|screenSize"
  android:theme="@android:style/Theme.Dialog" >
</activity>
```

##4.SDK機能の実装

C#スクリプトに定義した関数名からSDK機能の呼び出しを行います。

####4-1.アプリ起動時

アプリケーションの起動時にクライアントID・アプリケーションIDを設定する実装を行ってください。  
各種IDは弊社より別途（ヒアリングシート等で）ご連絡致します。

[実装例]

```c#
Orca.Configure(
  "86b8094d44c175850b18ec31eb9adf71", // クライアントID
  "d1d1b00e741d2417a4a1f55b1eda5bed"  // アプリケーションID
);

```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|クライアントID|必須|弊社で発行しご連絡致します。|
|第２引数|アプリケーションID|必須|弊社で発行しご連絡致します。|


####4-2.攻略情報取得

攻略情報を表示する以前のシーンが決定したタイミングで実行するよう実装を行ってください。  
シーンIDは弊社より別途（ヒアリングシート等で）ご連絡致します。

[実装例]

```c#
Orca.GetRecommendPage(
  "65928b3ceeb3e9cb24d917e5532ad332", // シーンID
  0,                                  // クリア情報
  "{'OptionalData':{'puid':'1234','pname':'山田太郎','level':'レベル1', ・・・ }}" // 任意項目
);

```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|シーンID|必須|弊社で発行しご連絡致します。|
|第２引数|クリア情報|必須|ゲームクリアの状態を設定して下さい。  （1:not complete 2:complete 3:before play）|
|第３引数|任意項目|任意|任意項目をJSON形式にて設定して下さい。 |

【任意項目について】  
設定可能な任意項目は下記をご覧ください。  
ユーザのゲームレベルに応じた攻略情報の配信を利用する場合、”level”は必須となります。  
任意項目は下記以外にも追加可能です。追加を希望する場合は、別途ご相談下さい。  
各項目は最大128桁まで設定可能です。  
任意項目にマルチバイト文字を使用する場合、スクリプトファイルの文字コードをUTF-8(BOM付き)に変更して下さい。

|任意項目名|key|設定内容|
|:------|:------|:------|
|ユーザID|puid   |ゲーム内のユーザIDを設定|
|ユーザ名|pname  |ゲーム内のユーザ名称を設定|
|レベル|level  |ユーザのレベル数値または名称を設定|
|ランク|rank  |ユーザのランク数値または名称を設定|
|経験値|exp  |ユーザの経験値数値または名称を設定|
|設定キャラクタ名|char  |設定しているキャラクタ名を設定|
|HP|hp  |キャラクタのHP数値または名称を設定|
|スタミナ|stm  |キャラクタのスタミナ数値または名称を設定|
|攻撃力|power  |キャラクタの攻撃力数値または名称を設定|
|利用もしくは設定装備/武器関連名|item  |キャラクタの装備・武器などの名称を設定|
|編成デッキキャラ名|dchar  |編成デッキキャラクタ名を設定|
|対戦ボスキャラ名|bchar  |ゲーム内のボスキャラ名称を設定|

任意項目を使用しない場合は、以下のように第３引数に空文字を設定してください。

```c#
Orca.GetRecommendPage(
  "65928b3ceeb3e9cb24d917e5532ad332", // シーンID
  0,                                  // クリア情報
  ""                                  // 任意項目を使用しない
);
```

####4-3.攻略情報表示チェック

攻略情報を表示する前に実行してステータスを確認してください。  
攻略情報が表示可能な場合はtrueを返します。  
表示不可能な場合はfalseを返します。（該当する攻略情報がない、対応していないOSバージョン等）  
ステータスがfalseの状態で、「4-4.攻略情報表示」を実行した場合は、攻略情報は表示されません。

[実装例]

```c#
if (Orca.IsRecommendPage("65928b3ceeb3e9cb24d917e5532ad332")) { // シーンID
  // 攻略情報が表示可能
  // 例えば、攻略情報表示ボタンを表示する処理コードを実装
} else {
  // 攻略情報が表示不可能
}
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|シーンID|必須|弊社で発行しご連絡致します。|

####4-4.攻略情報表示

攻略情報を表示する際に実行して下さい。

[実装例]

```c#
Orca.ShowRecommendPage(
  "65928b3ceeb3e9cb24d917e5532ad332" // シーンID
);
```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|シーンID|必須|弊社で発行しご連絡致します。|

####4-5.全体的な実装例（SDK機能の実行タイミング）

1. アプリ起動
2. 「4-1.アプリ起動」各種IDの設定
3. ユーザ様のプレイ開始アクション
4. 「4-2.攻略情報取得」
5. 「4-3.攻略情報表示チェック」

6-1. チェックがtrueの場合、ユーザ様がシーン終了時に攻略情報を見るボタンを設置する等の処理  
6-2. ユーザ様が攻略情報を見るアクション  
6-3. 「4-4.攻略情報表示」  

7-1. チェックがfalseの場合は、攻略情報がない場合の処理

##5.その他API

####5-1.クリエイティブ種別の取得

```c#
int creativeType = Orca.getCreativeType("65928b3ceeb3e9cb24d917e5532ad332"); // シーンID

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

##6.SDK導入後のテスト

####6-1.確認事項

マーケットへの申請までに、SDKを導入した状態でテストを行い、アプリケーションの動作に問題がないことを確認してください。

1. テスト用端末にて対象アプリを起動
1. **攻略情報**を呼び出す地点(シーンまたはステージ)まで画面遷移する
1. **攻略情報**を表示する
1. **攻略情報**を閉じる

弊社へテスト実行時間をお伝えください。正常にSDK機能が動作しているかログ等で確認致します。  
弊社側の確認にて問題がなければテスト完了となります。

####6-2.デバッグモードについて

テスト時の確認等の為にデバッグモードをご用意しております。

【デバッグモードの動作】

* 配信制御は行われず、サンプルサイト及びサンプル動画が配信されます。  
* 開発環境の標準出力にログが出力されます。

テスト完了後は、弊社にてデバッグモードを解除し運用モードとなります。

----

SDK導入に関してお困りの際は、下記のメール宛先にお問い合わせください。

問い合わせ先：z-mediadev@cyber-z.co.jp

