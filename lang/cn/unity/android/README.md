# Android项目的配置

## package结构
* Assets/
  * Plugins/
    * `Orca.cs`
    * Android/
      * `orca-androidsdk.jar`
      * `AndroidManifest.xml`
      * res/

UnityPackage中「Assets/Plugins」里面的文件对象导入到项目里。

* [Eclipse项目导入方法](/lang/cn/doc/integration/eclipse)

## 依赖库

贵公司的App如果没有使用下面的库，需要另外导入。

|名称|導入手順|
|:--|:--|
|Google Play Services|[官网](https://developers.google.com/android/guides/setup)  （不使用AdvertisingId的情况下可以不导入）|
|Android Asynchronous Http Client|下载[库](http://loopj.com/android-async-http/)拷贝到「Plugins」目录下。|
* [Google Play Services导入方法](/lang/cn/doc/google_play_services)
* [Android Asynchronous Http Client导入方法](/lang/cn/doc/async_http)

## 编辑AndroidManifest.xml

参照Assets/Plugins/Android/AndroidManifest.xml，拷贝以下内容。

### * 权限配置

　追加SDK运行的必要权限到AndroidManifest.xml。  
　&lt;Manifest>里追加下面的权限。

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

### * activity配置

```xml
<activity
  android:name="net.orcaz.sdk.unity.MainActivity"
  android:configChanges="orientation|keyboardHidden|screenSize"
  android:hardwareAccelerated="true">
</activity>
<activity
    android:name="net.orcaz.sdk.Orca"
    android:configChanges="keyboardHidden|orientation|screenSize"
    android:hardwareAccelerated="true" >
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
　追加SDK运行的必要权限到AndroidManifest.xml。   
　**对android:value设置适当的版本号。**

```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

## * 画面旋转配置

iOS App的情况下，与贵公司App画面旋转的设定保持一致。  
Android App的情况下，编辑`AndroidManifest.xml`的`android:configChanges`配置画面旋转


## 使用ProGuard的情况


使用ProGuard对程序进行代码混淆的时候，需要禁止对 ORCA SDK 的方法做代码混淆，请追加下面的配置。


```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

另外，导入GooglePlayServiceSDK的情况下，参考下面的页面配置keep值。

[Google Play Services导入时使用Proguard](https://developer.android.com/google/play-services/setup.html#Proguard)

----

[TOP](/lang/cn/unity/README-float.md)
