# みんなの攻略情報 iOS SDK導入手順

### 1-1. 動作環境
 * iOS 6.0 以降

### 1-2. 用語

 * **"シーン"**  
本プロジェクトでは表示する攻略情報の各クエスト、ステージ、イベント等を「シーン」と呼称します。

* **"ページ"**  
貴社アプリの攻略情報を呼び出す画面の単位を「ページ」と呼称します。

## 2. SDKのダウンロード

準備中

## 3. プロジェクトへの導入

貴社アプリのXCodeプロジェクトへOrcaSDKのフレームワークを追加します。

* [OrcaSDKの追加方法](./xcode/README.md)

## 依存ライブラリ

貴社アプリで以下のライブラリを利用されていない場合は導入が必要となります。

|名称|Status|
|:--|:--|
|AdSupport.framework|Required|
|Systemconfiguration.framework|Required|
|Security.framework|Required|

* [AdSupportの導入方法(SystemconfigurationとSecurityも同様)](./adsupport/README.md)


## Bridging Headerの編集（Swiftの場合）
SwiftにてOrcaSDKを利用するにはBridging Headerにてインポートする必要があります。
プロジェクトにBridging Headerがない場合は新たに作成します。

#### * Bridging Headerの例（ファイル名：Sample_Native_iOS-Bridging-Header.h）

```objectivec
#ifndef Sample_Native_iOS_Bridging_Header_h
#define Sample_Native_iOS_Bridging_Header_h

#import "OrcaSDK/Orca.h" // <- 既にブリッジヘッダがある場合はこの行を追加。

#endif /* Sample_Native_iOS_Bridging_Header_h */
```

新たにBridging Headerを作成した場合、プロジェクトに対してBridging Headerを指定する必要がありあります。

* [Bridging Headerの指定方法](./bridging_header/README.md)


## UIApplicationDelegateの編集（Swiftの場合）
XCodeによって出力されたプロジェクトに含まれるAppDelegate.m等、UIApplicationDelegateを継承したクラスへ以下のコードを追加します。

#### * applicationDidEnterBackground内にOrca.setBackground(true)を追加。

```swift
 func applicationDidEnterBackground(application: UIApplication) {
 	Orca.setBackground(true)	// <- 追加
 }
```

#### * applicationWillEnterForeground内にOrca.setBackground(false)を追加。

```swift
 func applicationWillEnterForeground(application: UIApplication) {
 	Orca.setBackground(false)	// <- 追加
 }
```

## UIApplicationDelegateの編集（Objective-Cの場合）
XCodeによって出力されたプロジェクトに含まれるAppDelegate.m等、UIApplicationDelegateを継承したクラスへ以下のコードを追加します。

#### * Orcaヘッダをインポート。（Objective-Cの場合）


```objectivec
 #import "OrcaSDK/Orca.h"
```

#### * -(void)applicationDidEnterBackground:内に[Orca setBackground:YES];を追加。

```objectivec
- (void)applicationDidEnterBackground:(UIApplication*)application
{
	[Orca setBackground:YES];	// <- 追加
	::printf("-> applicationDidEnterBackground()\n");
}
```

#### * -(void)applicationWillEnterForeground:内に[Orca setBackground:NO];を追加。

```objectivec
- (void)applicationWillEnterForeground:(UIApplication*)application
{
	[Orca setBackground:NO];	// <- 追加
	::printf("-> applicationWillEnterForeground()\n");
}
```

## 4. SDK機能の実装

### 4-1. アプリ起動時

**アプリケーションの起動時** にクライアントID・アプリケーションIDを設定する実装を行ってください。  
各種IDは弊社より別途（ヒアリングシート等で）ご連絡致します。

[実装例]（Swiftの場合）

```swift

override func viewDidLoad() {
    super.viewDidLoad()
      Orca.configure(
        "86b8094d44c175850b18ec31eb9adf71", 　　　　　　　　　// クライアントID
        applicationID: "d1d1b00e741d2417a4a1f55b1eda5bed"  // アプリケーションID
    )
}

```

|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|クライアントID|必須|弊社で発行しご連絡致します。<br/>|
|第２引数|アプリケーションID|必須|弊社で発行しご連絡致します。<br/>※ iOSとAndroid共通で利用可能|

### 4-2. 攻略情報フロートアイコン表示要求

貴社アプリ画面上に**攻略情報フロートアイコン**を表示したいタイミングで実行するよう実装を行ってください。  
ページID、シーンIDは弊社より別途（ヒアリングシート等で）ご連絡致します。  
ページIDの指定は任意です。ページIDを指定しない場合は以下の機能が行えません。  
・ページ単位での攻略情報の表示数等の集計  
・ページ単位でのフロートアイコンの変更  

[実装例]（Swiftの場合）

```swift
Orca.showFloatIcon(
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",        // ページID
  scene: "65928b3ceeb3e9cb24d917e5532ad332|afc4a607d40edbe7c51853ac3af0910f",  // シーンID（攻略情報が複数ある場合の例）
  level: "レベル1",                           // ユーザレベル
  exp: "15",                                 // ユーザ経験値
  chara: "勇者"                              // ユーザ設定キャラ
  optional: "{'OptionalData':{'puid':'1234','pname':'山田太郎','level':'レベル1', ・・・ }}" // 任意項目
);
```

[実装例（配信制御を利用しない場合）]（Swiftの場合）

```swift
Orca.showFloatIcon(
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",         // ページID
  scene: "65928b3ceeb3e9cb24d917e5532ad332",  // シーンID（攻略情報が1つだけの場合の例）
  level: "",                                  // ユーザレベル
  exp: "",                                    // ユーザ経験値
  chara: "",                                  // ユーザ設定キャラ
  optional: ""                                // 任意項目
);
```

#### 攻略情報フロートアイコンの表示を切り替える

貴社アプリの画面切り替え等のタイミングで、呼び出し地点のページID・シーンIDを指定し
「4-2. 攻略情報フロートアイコン表示要求」を実行してください。
既に表示中の攻略情報フロートアイコンを破棄し、新たにフロートアイコンを表示します。

#### 攻略情報フロートアイコンのデザインを設定する

ヒアリングシート等で、各ページでご使用するアイコンデザイン、サイズ、画像をお伺いの上、配信システムに登録致します。

#### 攻略情報フロートアイコンを非表示にする

一度呼び出した攻略情報フロートアイコンを非表示にするには、シーンIDを指定しない状態で
「4-2. 攻略情報フロートアイコン表示要求」を実行してください。


[実装例]（Swiftの場合）

```swift
Orca.showFloatIcon(
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",         // ページID
  level: "",                                  // シーンID（指定なし）
  level: "",                                  // ユーザレベル
  exp: "",                                    // ユーザ経験値
  chara: "",                                  // ユーザ設定キャラ
  optional: ""                                // 任意項目
);
```


|パラメータ|内容|必須|備考|
|:------|:------|:------|:------|
|第１引数|ページID|任意|弊社で発行しご連絡致します。<br/>※ iOSとAndroid共通で利用可能|
|第２引数|シーンID|任意|弊社で発行しご連絡致します。<br/>複数取得の場合は、" &#124; "区切りで設定（10個まで）<br/>指定なしの場合、表示中の攻略情報フロートアイコンを非表示にします。<br/>※ iOSとAndroid共通で利用可能|
|第３引数|ユーザレベル|任意|ユーザレベルで配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第４引数|ユーザ経験値|任意|ユーザ経験値で配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第５引数|ユーザ設定キャラ|任意|ユーザ設定キャラで配信制御を行う場合は設定してください。<br/>（設定しない場合は空文字を設定）|
|第６引数|任意項目|任意|任意項目をJSON形式にて設定して下さい。 <br/>（設定しない場合は空文字を設定）|

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


<a name="sample"></a>
### 4-3. 全体的な実装例（SDK機能の実行タイミング）

1. アプリ起動
2. 「4-1.アプリ起動」各種IDの設定
3. ユーザ様のプレイ開始アクション
4. 「4-2.攻略情報フロートボタン表示要求」
5. ユーザ様が攻略情報フロートアイコンをタップ
6. 攻略情報を表示

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
