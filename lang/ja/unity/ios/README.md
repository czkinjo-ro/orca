# iOSプロジェクトの設定

## パッケージ構成
* Assets/
  * Plugins/
    * `Orca.cs`
    * iOS/
      * `OrcaSDK.framework`

UnityPackage内の「Assets/Plugins」配下のファイルを対象プロジェクトに組み込んで下さい。

## 依存ライブラリ

貴社アプリで以下のライブラリを利用されていない場合は導入が必要となります。

|名称|Status|
|:--|:--|
|AdSupport.framework|Required|
|Systemconfiguration.framework|Required|
|Security.framework|Required|
* [AdSupportの導入方法(SystemconfigurationとSecurityも同様)](./adsupport/README.md)

## App Transport Security の例外設定

Unityによって出力されたXcodeのプロジェクト設定変更

* [App Transport Security の例外設定](/lang/ja/doc/ats)

## UnityAppController.mmの編集
Unityによって出力されたXcodeのプロジェクトに含まれるUnityAppController.mmに
以下のコードを追加します。

#### * Orcaヘッダをインポート。


```objective-c
 #import "OrcaSDK/Orca.h"
```

#### * -(void)applicationDidEnterBackground:内に[Orca setBackground:YES];を追加。

```objective-c
- (void)applicationDidEnterBackground:(UIApplication*)application
{
	[Orca setBackground:YES];	// <- 追加
	::printf("-> applicationDidEnterBackground()\n");
}
```

#### * -(void)applicationWillEnterForeground:内に[Orca setBackground:NO];を追加。

```objective-c
- (void)applicationWillEnterForeground:(UIApplication*)application
{
	[Orca setBackground:NO];	// <- 追加
	::printf("-> applicationWillEnterForeground()\n");
}
```

----

[TOP](/lang/ja/unity/README.md)
