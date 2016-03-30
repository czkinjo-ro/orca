# iOS项目配置

## package结构
* Assets/
  * Plugins/
    * `Orca.cs`
    * iOS/
      * `OrcaSDK.framework`

UnityPackage中「Assets/Plugins」里面的对象导入到项目里。

## 依赖库

贵公司的App如果没有使用下面的库，需要另外导入。

|名称|Status|
|:--|:--|
|AdSupport.framework|Required|
|Systemconfiguration.framework|Required|
|Security.framework|Required|
* [AdSupport的导入方法(Systemconfiguration和Security同样)](./adsupport/README.md)

## 關於App Transport Security的無效設置
* [關於App Transport Security的無效設置](/lang/ja/doc/ats)

## 编辑UnityAppController.mm
在从Unity导出的Xcode项目中的UnityAppController.mm文件中写入下面代码。
#### * 导入Orca头文件。


```objective-c
 #import "OrcaSDK/Orca.h"
```

#### * -(void)applicationDidEnterBackground:里追加[Orca setBackground:YES];

```objective-c
- (void)applicationDidEnterBackground:(UIApplication*)application
{
	[Orca setBackground:YES];	// <- 追加
	::printf("-> applicationDidEnterBackground()\n");
}
```

#### * -(void)applicationWillEnterForeground:里追加[Orca setBackground:NO];
```objective-c
- (void)applicationWillEnterForeground:(UIApplication*)application
{
	[Orca setBackground:NO];	// <- 追加
	::printf("-> applicationWillEnterForeground()\n");
}
```

----

[TOP](/lang/cn/unity/README-float.md)
