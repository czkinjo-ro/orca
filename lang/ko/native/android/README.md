# 모두의 공략정보 Android SDK의 도입 방법

### 1-1. 동작 환경
 * Android 4.2.2 이상

### 1-2. 용허

* **"scene"**  
본 프로젝트에서는 공략 정보를 표시 할 각각의 퀘스트, 스테이지, 이벤트 등을 "scene"이라 호칭합니다.

## 2. SDK다운로드

준비중입니다.

## 3. 프로젝트에 도입

* [Eclipse프로젝트에 도입 방법](/lang/ko/doc/integration/eclipse)

## 의존 라이브러리

귀사의 앱에서 다음의 라이브러리를 사용하지 않는 경우 도입이 필요합니다.

|명칭|도입 방법|
|:--|:--|
|Google Play Services|[정보 사이트](https://developers.google.com/android/guides/setup)  （AdvertisingId를 이용하지 않는 경우는 필요 없음）|
|Android Asynchronous Http Client|[다운로드](http://loopj.com/android-async-http/)「libs」디렉토리 아래에 복사 하십시오.|
* [Google Play Services도입 방법](/lang/ko/doc/google_play_services)
* [Android Asynchronous Http Client도입 방법](/lang/ko/doc/async_http)

## AndroidManifest.xml 편집

AndroidManifest.xml을 참조하여 다음의 내용을 복사하십시오.

### * 퍼미션 설정

　SDK의 동작에 필요한 퍼미션을AndroidManifest.xml에 추가합니다.  
　&lt;Manifest>태그 내에 다음의 퍼미션의 설정을 추가합니다.

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### * activity의 설정

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

### * Google Play Services를 이용하기 위한 설정
　SDK동작에 필요한 아래의 메타데이터를AndroidManifest.xml에 추가하십시오.   
　**android:value의 값은 적절한 버전을 설정하여 주십시오.**

```xml
<meta-data android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
```

## ProGuard를 이용하는 경우

ProGuard를 이용하여 앱의 난독화 처리를 할 때는 모두의 공략정보SDK의 메소드가 대상이되지 않도록 아래의 설정을 추가 하십시오.

```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

또한、GooglePlayServiceSDK를 도입했을 경우、아래의 페이지에 keep지정이 기술되어있는지 확인하여 주십시오.

[Google Play Services도입시의 Proguard대응](https://developer.android.com/google/play-services/setup.html#Proguard)


## 4. SDKSDK기능의 구현

### 4-1. 앱 기동시

**앱 기동시** 클라이언트ID・앱ID를 설정하여 구현하십시오.  
각종ID는 폐사에서 별도（히어링 시트 등으로）연락 드립니다.

[구현 예]

```java

import net.orcaz.sdk.Orca;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    Orca.configure(
      this,                               // activity
      "86b8094d44c175850b18ec31eb9adf71", // 클라이언트ID
      "d1d1b00e741d2417a4a1f55b1eda5bed"  // 앱ID
    );
}
```

|매개변수|내용|필수|비고|
|:------|:------|:------|:------|
|제１인수|activity|필수|앱의 activity를 설정하십시오.<br/>|
|제２인수|클라이언트ID|필수|폐사에서 발행하여 연락드립니다.<br/>|
|제３인수|앱ID|필수|폐사에서 발행하여 연락드립니다.<br/>※ iOS、Android공통으로 이용가능|

### 4-2. 공략 정보 취득


공략 정보를 표시하는 이전의 scene이 결정된 타이밍에 실행되도록 구현해야합니다.  
sceneID는 폐사에서 별도(히어링 시트 등으로) 연락드립니다.

[구현 예]

```java
Orca.getRecommendPage(
  this,                               // activity
  "65928b3ceeb3e9cb24d917e5532ad332|afc4a607d40edbe7c51853ac3af0910f",  // sceneID (2개 취득의 경우의 예)
  "レベル1",                           // 사용자 레벨
  "15",                                // 사용자 경험치
  "勇者"                               // 사용자 설정 캐릭터
);
```

[구현 예 (전송 제어를 사용하지 않을 경우)]

```java
Orca.getRecommendPage(
  this,                                // activity
  "65928b3ceeb3e9cb24d917e5532ad332",  // sceneID (1개 취득 경우의 예)
  "",                                  // 사용자 레벨
  "",                                  // 사용자 경험치
  ""                                   // 사용자 설정 캐릭터
);
```

| 매개변수 | 내용 | 필수 | 비고 |
|:------|:------|:------|:------|
| 제 1 인수| activity | 필수 | 앱의 activity를 설정<br/>|
| 제 2 인수 | sceneID | 필수 | 폐사에서 발행하여 연락드립니다. <br/> 복수 scene 취득의 경우에는 " &#124; "로 구분하여 설정 (10개까지) <br/>※ iOS、Android공통으로 이용가능|
| 제 3 인수 | 사용자 레벨 | 임의 | 사용자 레벨에서 전송 제어를 할 경우 설정하십시오. <br/> (설정하지 않는 경우는 ""을 설정) |
| 제 4 인수 | 사용자 경험치 | 임의 | 사용자 경험치로 전송 제어를 할 경우 설정하십시오. <br/> (설정하지 않는 경우는 ""을 설정) |
| 제 5 인수 | 사용자 설정 캐릭터 | 임의 | 사용자 설정 캐릭터로 전송 제어를 할 경우 설정하십시오. <br/> (설정하지 않는 경우는 ""을 설정) |

### 4-3. 공략 정보 표시 체크

공략 정보를 표시하기 전에 실행하여 상태를 확인하십시오.  

[구현 예]

```java
int result = Orca.checkRecommendPage("65928b3ceeb3e9cb24d917e5532ad332");
if (result == 1) {
  // 공략 정보 표시 가능
  // 예를 들어, 공략 정보 표시 버튼을 표시하는 처리 코드를 구현
} else if (result == 2) {
  // 공략 정보를 표시 할 준비가되어 있지 않음
  // 예를 들어, 다시 GetRecommendPage를 호출하는 코드를 구현
} else if (result == 3) {
  // 공략 정보 표시 불가능
}
```


| 매개변수 | 내용 | 필수 | 비고 |
|:------|:------|:------|:------|
| 제 1 인수 | sceneID | 필수 | 폐사에서 발행하여 연락드립니다. <br/> 여러 scene의 체크는 할수 없습니다. <br/>※ iOS、Android공통으로 이용가능|

| 반환 값(상태) | 내용 |
|:------|:------|
| 1 | 공략 정보 표시 가능 |
| 2 | 공략 정보를 표시 할 준비가되어 있지 않음 <br/> (준비한 공략 정보의 유효 기간 (디폴트 30분)의 만료 등) |
| 3 | 공략 정보 표시 불가능 <br/> (초기화 설정이 성공하지 못했거나 지원하지 않는 OS버전, 예상치 못한 불일치 등) |

[주의 사항]  
* 상태가 "2"이면 다시 「4-2 공략 정보 취득」을 실행하여 공략 정보를 검색 할 수 있습니다.    
단, 전송 제어 등에 의해 표시할 후보가없는 경우 재 취득을 실행해도 상태는 변화하지 않습니다.  
* 상태가 "2"또는 "3"상태에서 「4-4 공략 정보보기」를 실행하면 공략 정보는 표시되지 않고,  
"現在メンテナンス中です。" 팝업이 표시됩니다.

### 4-4. 공략 정보 표시

공략 정보를 표시 할 때 사용하십시오.

[구현 예]

```java
Orca.showRecommendPage(
  this,                               // activity
  "65928b3ceeb3e9cb24d917e5532ad332", // sceneID
  0,                                  // 클리어 정보
  "{'OptionalData':{'puid':'1234','pname':'山田太郎','level':'レベル1', ・・・ }}", // 임의 항목
  null
);
```

[구현 예 (전송 제어를 사용하지 않을 경우)]

```java
Orca.showRecommendPage(
  this,                               // activity
  "65928b3ceeb3e9cb24d917e5532ad332", // sceneID
  0,                                  // 클리어 정보
  "",                                 // 임의 항목을 사용하지 않음
  null
);
```

| 매개변수 | 내용 | 필수 | 비고 |
|:------|:------|:------|:------|
| 제 1 인수 | activity | 필수 | 앱의 activity를 설정<br/> |
| 제 2 인수 | sceneID | 필수 | 폐사에서 발행하여 연락드립니다. <br/>※ iOS、Android공통으로 이용가능|
| 제 3 인수 | 클리어 정보 | 필수 | 게임 클리어 상태를 설정하십시오. <br/> (1:not complete 2:complete 3:before play) |
| 제 4 인수 | 임의 항목 | 임의 | 임의 항목을 JSON 형식으로 설정하십시오. <br/> (설정하지 않는 경우는 ""로 설정) |

[임의 항목에 대해]  
설정 가능한 임의 항목은 다음을 참조하십시오.  
임의 항목은 다음 이외에도 추가 할 수 있습니다. 다음 항목 이외를 추가 하고자하는 경우 별도 상담 바랍니다.  
각 항목은 최대 128자리까지 설정 가능합니다.  
임의 항목에 멀티 바이트 문자를 사용하는 경우 스크립트 파일의 문자 코드를 UTF-8(BOM 포함)으로 변경하십시오.

| 모든 항목 이름 | key | 설정 내용|
|:------|:------|:------|
| 사용자 ID | puid | 게임내의 사용자 ID를 설정 |
| 사용자 명 | pname | 게임내의 사용자 명칭을 설정 |
| 대전 보스 캐릭터 이름 | bchar | 게임내의 보스 캐릭터 이름을 설정 |
| 레벨(랭크) | level | 사용자 레벨 숫자 또는 이름을 설정 |
| 경험치 | exp | 사용자의 경험치 수치 또는 명칭을 설정 |
| 설정 캐릭터 이름 | char | 설정된 캐릭터 이름을 설정 |
| HP | hp | 캐릭터의 HP 수치 또는 명칭을 설정 |
| 스태미너 | stm | 캐릭터의 체력 수치 또는 명칭을 설정 |
| 공격력 | power | 캐릭터의 공격력 수치 또는 명칭을 설정 |
| 이용 또는 설정 장비/무기 관련 이름 | item | 캐릭터의 장비·무기 등의 명칭을 설정 |

<a name="sample"></a>
### 4-5. 전체적인 구현 예(SDK 기능의 실행 타이밍)

1. 앱 시작
2. 「4-1. 앱 시작」각종 ID의 설정
3. 사용자의 플레이 시작 액션
4. 「4-2 공략 정보 취득」
5. 「4-3 공략 정보 표시 체크」

6-1. 「4-3 공략 정보 표시 체크」가 "1"의 경우, 사용자에 의해서 scene 종료시 공략 정보를 보는 버튼을 설치하는 등의 처리</br>
6-2. 「4-3 공략 정보 표시 체크」가 "2"의 경우 4단계로 돌아가 다시 공략 정보를 취득</br>
6-3. 「4-3 공략 정보 표시 체크」가 "3"의 경우는 공략 정보가없는 경우의 처리

7-1. 사용자에 의해서 공략 정보보기 액션(버튼 탭 등)
7-2. 「4-4 공략 정보 표시」  

## 5. 기타 API

### 5-1. 크리에이티브 종류의 취득

```java
int creativeType = Orca.getCreativeType("65928b3ceeb3e9cb24d917e5532ad332"); // sceneID
```

| 매개변수 | 내용 | 필수 | 비고 |
|:------|:------|:------|:------|
| 제 1 인수 | sceneID | 필수 | 당사에서 발행하여 연락드립니다. <br/>※ iOS、Android공통으로 이용가능|


| 반환 값(크리에이티브 종류) | 내용 |
|:------|:------|
| 0 | 모두의 공략정보 SDK 의 외침 에 실패 |
| 1 | 공략 사이트만 있는경우 |
| 2 | 공략 동영상만 있는경우 |
| 3 | 돌다 있는경우 |

공략 정보에 동영상이 포함 된 경우, 게임 내에서 재생되는 BGM을 중지 할 것을 권장합니다.

## 5. SDK 도입후 테스트

### 5-1. 확인 사항

스토어 신청전까지 SDK를 도입 한 상태에서 테스트를 실시하여 앱의 동작에 문제가 없는지 확인하십시오.

1. 테스트 용 단말에서 대상 앱을 기동
2. **공략 정보** 를 호출 지점(scene 또는 스테이지)까지 화면 전환하기
3. **공략 정보** 보기
4. **공략 정보** 닫기

폐사에 테스트 실행 시간을 알려주십시오. 제대로 SDK 기능이 작동하고 있는지 로그 등으로 확인합니다.  
폐사측의 확인에서 문제가 없다면 테스트가 완료됩니다.

### 5-2. 디버그 모드에 대해

테스트시의 확인 등을 위하여 디버그 모드를 준비하고 있습니다.

[디버그 모드 동작]

* 전송 제어가 되지 않으며, 샘플 사이트 및 동영상이 전송됩니다.
* 개발 환경의 표준 출력으로 로그가 출력됩니다.

|디버그 모드 scene|sceneID|
|:------|:------|
|scene 1|fc7f24abc577f37d44b6fb0095dcd976|
|scene 2|aee4857751950eee5441057d436e506a|
|scene 3|ea0a160b2a496c33efe0c9511f61b34f|
|scene 4|c0f7d3256b91dc80faab2d0ca34696e6|
|scene 5|a3c9cc0625a56a2f9abb1400f2e17f80|
|scene 6|46d8ca247c7cdd3f81c0649b1bc3c2fc|
|scene 7|f576372b68c175cb52730f05a510eb86|
|scene 8|3ae0fa5850a7ef180b56617f271124a7|
|scene 9|a353d25ddc95549b6bb848fbf6401510|
|scene 10|a63b500d519653e2306c77213ef91eb9|

테스트 완료 후 폐사에서 디버그 모드를 해제하여 운용 모드로 전환합니다.

----

SDK 도입에 대해 상세한 상담은 원하시면 아래 메일 주소로 문의해 주십시오.

문의처 : z-mediadev@cyber-z.co.jp
