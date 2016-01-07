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

|名称|導入手順|
|:--|:--|
|AdSupport.framework|[情報サイト](https://developer.apple.com/library/tvos/documentation/DeviceInformation/Reference/AdSupport_Framework/index.html)  （AdvertisingIdを利用しない場合は必要なし）|

* [AdSupportの導入方法](./adsupport/README.md)

## UnityAppController.mmの編集
Unityによって出力されたXcodeのプロジェクトに含まれるUnityAppController.mmに
以下のコードを追加します。

#### * Orcaヘッダをインポート。


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

----

[TOPへ](/lang/ja/unity/README.md)
