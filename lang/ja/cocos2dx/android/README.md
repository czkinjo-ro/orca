# Androidプロジェクトの設定

## パッケージ構成

* Androidプロジェクト/
  * Classes/
    * `CCOrca.h`
    * `CCOrca.cpp`
  * proj.android/
    * libs
      * `orca-androidsdk.jar`


パッケージ内のファイルを対象プロジェクトに組み込んで下さい。
  * Classes配下に`CCOrca.h`と`CCOrca.cpp`をコピーしてください。
  * proj.android/libs配下に`orca-androidsdk.jar`をコピーしてください。

##### [Eclipseプロジェクトへの導入方法](/lang/ko/doc/integration/eclipse)

### Android.mkの編集

proj.android/jni/Android.mkの`LOCAL_SRC_FILES`に`CCOrca.cpp`を追加してください。

```mk
LOCAL_SRC_FILES := ...
                   ...  //省略
                   ../../Classes/CCOrca.cpp
```

### CCOrca.cppのインクルード指定

CCOrca.cppのJniHelper.hのインクルードパスを開発環境に合わせて編集してください。

 ```c++
#include "cocos2d.h"
#include "CCOrca.h"
#include "platform/android/jni/JniHelper.h"
 ```

## 依存ライブラリ

貴社アプリで以下のライブラリを利用されていない場合は導入が必要となります。

|名称|必須|導入手順|
|:--|:--|:--|
|Google Play Services|任意|[情報サイト](https://developers.google.com/android/guides/setup)  （AdvertisingIdを利用しない場合は必要なし）|
|Android Asynchronous Http Client|必須|[ダウンロード](http://loopj.com/android-async-http/)「Plugins」ディレクトリ配下に設置してください。|
* [Google Play Servicesの導入方法](/lang/ko/doc/google_play_services)
* [Android Asynchronous Http Clientの導入方法](/lang/ko/doc/async_http)

## AndroidManifest.xmlの編集

### * パーミッションの設定

　SDKの動作に必要な以下のパーミッションをAndroidManifest.xmlに追加します。

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
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:hardwareAccelerated="true" >
</activity>        
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
<activity
        android:name="net.orcaz.sdk.floating.ContentsActivity"
        android:configChanges="keyboardHidden|orientation|screenSize"
        android:hardwareAccelerated="true" >
</activity>
<service android:name="net.orcaz.sdk.floating.FloatService" />
```

### * Google Play Servicesを利用するための設定
　SDKの動作に必要な以下のメターデータをAndroidManifest.xmlに追加してください。
```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

## 画面回転の設定

Androidアプリの場合、画面の回転の設定については`AndroidManifest.xml`の`android:configChanges`を編集してください。

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

----
