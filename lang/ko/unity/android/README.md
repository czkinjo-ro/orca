# Android프로젝트의 설정

## 패키지 구성
* Assets/
  * Plugins/
    * `Orca.cs`
    * Android/
      * `orca-androidsdk.jar`
      * `AndroidManifest.xml`
      * res/

UnityPackage내의「Assets/Plugins」아래에 파일을 대상 프로젝트에 추가해 주십시오.

* [Eclipse프로젝트의 도입방법](/lang/ko/doc/integration/eclipse)

## 의존 라이브러리

귀사의 앱에서 다음의 라이브러리를 사용하지 않는 경우 도입이 필요합니다.

|명칭|도입 방법|
|:--|:--|
|Google Play Services|[정보사이트](https://developers.google.com/android/guides/setup)  （AdvertisingId를 이용하지 않는 경우는 필요 없음）|
|Android Asynchronous Http Client|[다운로드](http://loopj.com/android-async-http/)「Plugins」디렉토리 아래에 복사 하십시오.|
* [Google Play Services의 도입 방법](/lang/ko/doc/google_play_services)
* [Android Asynchronous Http Client의 도입 방법](/lang/ko/doc/async_http)

## AndroidManifest.xml의 수정

Assets/Plugins/Android/AndroidManifest.xml를 참조하여、아래의 내용을 복사해 주십시오.

### * 퍼미션의 설정

　SDK의 동작에 필요한 아래의 퍼미션을 AndroidManifest.xml에 추가합니다.

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
```

### * activity의 설정

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
### * Google Play Services를 이용하기 위한 설정
　SDK동작에 필요한 아래의 메타데이터를AndroidManifest.xml에 추가하십시오.  
　**android:value의 값은 적절한 버전을 설정하여 주십시오.**

```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

## 화면회전의 설정

Android의 경우、화면전환 설정에 대해서는`AndroidManifest.xml`の`android:configChanges`를 수정하여주십시오.

## ProGuard를 이용하는 경우

ProGuard를 이용하여 앱의 난독화 처리를 할 때는 모두의 공략정보SDK의 메소드가 대상이되지 않도록 아래의 설정을 추가 하십시오.

```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

또한、GooglePlayServiceSDK를 도입했을 경우、아래의 페이지에 keep지정이 기술되어있는지 확인하여 주십시오.

[Google Play Services도입시의 Proguard대응](https://developer.android.com/google/play-services/setup.html#Proguard)

----

[TOP](/lang/ja/unity/README.md)
