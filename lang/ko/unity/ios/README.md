# iOS프로젝트의 설정

## 패키지의 구성
* Assets/
  * Plugins/
    * `Orca.cs`
    * iOS/
      * `OrcaSDK.framework`

UnityPackage내의「Assets/Plugins」아래의 파일을 귀사의 프로젝트에 포함 시키십시오.

## 의존 라이브러리

귀사의 앱에서 다음의 라이브러리를 사용하지 않는 경우 도입이 필요합니다.

|명칭|Status|
|:--|:--|
|AdSupport.framework|Required|
|Systemconfiguration.framework|Required|
|Security.framework|Required|
* [AdSupport의 도입 방법(SystemconfigurationとSecurity도 동일)](./adsupport/README.md)

## App Transport Security의 예외설정
Unity로부터 output된 Xcode프로젝트 설정

* [App Transport Security의 예외설정](/lang/ja/doc/ats)

## UnityAppController.mm의 수정
Unity에서 output된 Xcode의 프로젝트에 포함되어있는UnityAppController.mm에
다음의 코드를 추가합니다.

#### * Orca헤더의 import

```objective-c
 #import "OrcaSDK/Orca.h"
```

#### * -(void)applicationDidEnterBackground:내의 [Orca setBackground:YES];를 추가

```objective-c
- (void)applicationDidEnterBackground:(UIApplication*)application
{
	[Orca setBackground:YES];	// <- 추가
	::printf("-> applicationDidEnterBackground()\n");
}
```

#### * -(void)applicationWillEnterForeground:内に[Orca setBackground:NO];를 추가

```objective-c
- (void)applicationWillEnterForeground:(UIApplication*)application
{
	[Orca setBackground:NO];	// <- 추가
	::printf("-> applicationWillEnterForeground()\n");
}
```

----

[TOP](/lang/ko/unity/README.md)
