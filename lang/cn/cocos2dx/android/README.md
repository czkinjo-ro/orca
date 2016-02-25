# Androidプロジックトの設定

## パッケージ構成

* Androidプロジェクト/
  * Classes/
    * `OrcaPlugin.h`
    * `OrcaPlugin.cpp`

Cocos2dxPackage内の「orca-androidsdk.jar」をプロジェクトに組み込んでください。

* [Eclipseプロジェクトへの導入方法](/lang/ja/doc/integration/eclipse)

### * Android/Classed配下にOrcaPlugin.hとOrcaPlugin.cppをコピーしてください。

### * プロジェクト内配下のjni/Android.mk　を以下のように編集してください。

```mk
LOCAL_SRC_FILES := ...
                   ...  //省略
                   ../../Classes/OrcaPlugin.cpp
```
LOCAL_SRC_FILESに「Classes/OrcaPlugin.cpp」追加

### * OrcaPlugin.cppのJniHelper.hのinclude PATHを開発環境に合わせて編集してください。

 ```c++
#include "cocos2d.h"
#include "OrcaPlugin.h"
#include "platform/android/jni/JniHelper.h"
 ```

## 依存ライブラリ

貴社アプリで以下のライブラリを利用されていない場合は導入が必要となります。

|名称|必須|導入手順|
|:--|:--|:--|
|Google Play Services|任意|[情報サイト](https://developers.google.com/android/guides/setup)  （AdvertisingIdを利用しない場合は必要なし）|
|Android Asynchronous Http Client|必須|[ダウンロード](http://loopj.com/android-async-http/)「Plugins」ディレクトリ配下に設置してください。|
* [Google Play Servicesの導入方法](/lang/ja/doc/google_play_services)
* [Android Asynchronous Http Clientの導入方法](/lang/ja/doc/async_http)

## AndroidManifest.xmlの編集

### * パーミッションの設定

　SDKの動作に必要な以下のパーミッションをAndroidManifest.xmlに追加します。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### * アクティビティの設定
　SDKの動作に必要な以下のアクティビティをAndroidManifest.xmlに追加してください。
```xml
<activity
  android:name="net.orcaz.sdk.cocos2dx.Cocos2dxActivity"
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

### * Google Play Servicesを利用するための設定
　SDKの動作に必要な以下のメターデータをAndroidManifest.xmlに追加してください。
```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

## ProGuardを利用する場合

ProGuard を利用してアプリケーションの難読化を行う際は ORCA SDK のメソッドが対象とならない
よう、以下の設定を追加してください。

```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

また、GooglePlayServiceSDKを導入されている場合、以下のページで記載されているkeep指定が記述されているかご確認ください。

[Google Play Services導入時のProguard対応](https://developer.android.com/google/play-services/setup.html#Proguard)

----

[TOPへ](/lang/ja/cocos2dx/README.md)
