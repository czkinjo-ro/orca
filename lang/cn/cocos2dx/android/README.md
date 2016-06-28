# Android项目的配置

## package结构

* Android项目/
  * Classes/
    * `CCOrca.h`
    * `CCOrca.cpp`
  * proj.android/
    * libs
      * `orca-androidsdk.jar`


请把Package内的文件导入到对象项目内。
  * 请拷贝`CCOrca.h`と`CCOrca.cpp`到Classes下面。
  * 请拷贝`orca-androidsdk.jar`到proj.android/libs下面。

##### [Eclipse项目导入方法](/lang/cn/doc/integration/eclipse)

### Android.mk的编辑

请添加`CCOrca.cpp`到proj.android/jni/Android.mk的`LOCAL_SRC_FILES`里。

```mk
LOCAL_SRC_FILES := ...
                   ...  //省略
                   ../../Classes/CCOrca.cpp
```

### CCOrca.cpp的安装

请按照开发环境来编辑CCOrca.cpp的JniHelper.h的include路径。

 ```c++
#include "cocos2d.h"
#include "CCOrca.h"
#include "platform/android/jni/JniHelper.h"
 ```

## 依存类库

贵公司的App如果没有使用下面的库，需要另外导入。

|名称|必须|導入手順|
|:--|:--|:--|
|Google Play Services|任意|[官网](https://developers.google.com/android/guides/setup)  （不使用AdvertisingId的情况下可以不导入）|
* [Google Play Services导入方法](/lang/cn/doc/google_play_services)

## 编辑AndroidManifest.xml

### * 权限配置

　追加SDK运行的必要权限到AndroidManifest.xml里。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

### * activity配置

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

### * 使用Google Play Services情况下的配置
　追加SDK运行的必要meta-data到AndroidManifest.xml里。
```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

## * 画面旋转配置

Android App的情况下，编辑`AndroidManifest.xml`的`android:configChanges`配置画面旋转

## 使用ProGuard的情况

使用ProGuard对程序进行代码混淆的时候，需要禁止对大家的游戏攻略SDK的方法做代码混淆，请追加下面的配置。


```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

另外，导入GooglePlayServiceSDK的情况下，参考下面的页面配置keep值。

[Google Play Services导入时使用Proguard](https://developer.android.com/google/play-services/setup.html#Proguard)

----
