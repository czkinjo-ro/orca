# 大家的游戏攻略 iOS SDK 导入手册

### 1-1. 运行环境
 * iOS 6.0 以上

### 1-2. 术语

 * **"scene"**  
这个项目中游戏攻略的各个任务，关卡、活动等统称为“scene”。

* **"page"**  
贵公司游戏中打开的攻略画面被称为"page"。

## 2. 下载SDK

正在准备

## 3. 导入到项目

把OracSDK程序导入到贵公司的XCode项目中

* [OrcaSDK导入方法](./xcode/README.md)

## 依赖库

贵公司的App如果没有使用下面的库，需要另外导入。

|名称|Status|
|:--|:--|
|AdSupport.framework|Required|
|Systemconfiguration.framework|Required|
|Security.framework|Required|
* [AdSupport的导入方法(Systemconfiguration和Security同样)](./adsupport/README.md)


## 编辑Bridging Header（Swift）
在Swift项目中使用OrcaSDK需要导入Bridging Header。
项目中没有Bridging Header的情况下需要创建Bridging Header。

#### * Bridging Header的例子（文件名：Sample_Native_iOS-Bridging-Header.h）

```objectivec
#ifndef Sample_Native_iOS_Bridging_Header_h
#define Sample_Native_iOS_Bridging_Header_h

#import "OrcaSDK/Orca.h" // <- 已经有Bridging Header的情况下追加这一行。

#endif /* Sample_Native_iOS_Bridging_Header_h */
```

创建Bridging Header的情况下，对项目需要制定Bridging Header。

* [Bridging Header指定方法](./bridging_header/README.md)


## 编辑UIApplicationDelegate（Swift）
从XCode导出的项目里继承自AppDelegate.m、UIApplicationDelegate等类的类文件中追加下面的代码。

#### * 追加applicationDidEnterBackground内にOrca.setBackground(true)。

```swift
 func applicationDidEnterBackground(application: UIApplication) {
 	Orca.setBackground(true)	// <- 追加
 }
```

#### * applicationWillEnterForeground内にOrca.setBackground(false)を追加。

```swift
 func applicationWillEnterForeground(application: UIApplication) {
 	Orca.setBackground(false)	// <- 追加
 }
```

## 编辑UIApplicationDelegate（Objective-C）
从XCode导出的项目里继承自AppDelegate.m、UIApplicationDelegate等类的类文件中追加下面的代码。

#### * Orcaヘッダをインポート。（Objective-C）


```objectivec
 #import "OrcaSDK/Orca.h"
```

#### * 追加-(void)applicationDidEnterBackground:内に[Orca setBackground:YES];。

```objectivec
- (void)applicationDidEnterBackground:(UIApplication*)application
{
	[Orca setBackground:YES];	// <- 追加
	::printf("-> applicationDidEnterBackground()\n");
}
```

#### * 追加-(void)applicationWillEnterForeground:内に[Orca setBackground:NO];。

```objectivec
- (void)applicationWillEnterForeground:(UIApplication*)application
{
	[Orca setBackground:NO];	// <- 追加
	::printf("-> applicationWillEnterForeground()\n");
}
```

## 4. SDK功能使用

### 4-1. App启动

**程序启动时** 请设置clientID和applicationID。  
ID的发行请联系本社。


[导入实例]（Swift）

```swift

override func viewDidLoad() {
    super.viewDidLoad()
      Orca.configure(
        "86b8094d44c175850b18ec31eb9adf71", 　　　　　　　　　// clientID
        applicationID: "d1d1b00e741d2417a4a1f55b1eda5bed"  // applicationID
    )
}

```


|参数|内容|必须|备注|
|:------|:------|:------|:------|
|第一个参数|clientID|必須|联系本社发行。<br/>|
|第二个参数|applicationID|必須|联系本社发行。<br/>※ iOS和Android通用|

### 4-2. 显示游戏攻略的浮动图标

想在贵公司的游戏画面上显示**游戏攻略的浮动图标**时，执行以下步骤。  
pageID、sceneID请联系本社发行。  
pageID可以设置也可以不设置，不设置的情况下不提供下面的功能。  
・统计每个page的攻略访问次数  
・每个page的浮动图标变更  

[导入实例]（Swiftの場合）

```swift
Orca.showFloatIcon(
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",        // pageID
  scene: "65928b3ceeb3e9cb24d917e5532ad332|afc4a607d40edbe7c51853ac3af0910f",  // sceneID（有多个攻略的情况）
  level: "レベル1",                           // 用户等级
  exp: "15",                                  // 用户经验值
  chara: "勇者"                              // 用户角色
  optional: "{'OptionalData':{'puid':'1234','pname':'山田太郎','level':'レベル1', ・・・ }}" // 任意项目
);
```

[実装例（配信制御を利用しない場合）]（Swiftの場合）

```swift
Orca.showFloatIcon(
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",         // pageID
  scene: "65928b3ceeb3e9cb24d917e5532ad332",  // sceneID（有多个攻略的情况）
  level: "",                                  // 用户等级
  exp: "",                                    // 用户经验值
  chara: "",                                  // 用户角色
  optional: ""                                // 任意项目
);
```


#### 攻略浮动图标变更

游戏画面切换时，调用函数并指定pageID、sceneID，参考「4-2. 显示游戏攻略的浮动图标」。
已经显示在画面上的图标会被删除，显示新的浮动图标。

#### 自定义攻略浮动图标

准备好每个page图标的设计、尺寸、图片，在本公司的系统页面上传。

#### 隐藏示攻略浮动图标

想隐藏已经显示在画面上的攻略浮动图标的话，不指定scenceID再执行「4-2. 显示游戏攻略的浮动图标」的步骤。


[実装例]（Swiftの場合）

```swift
Orca.showFloatIcon(
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",         // pageID
  level: "",                                  // sceneID（有多个攻略的情况））
  level: "",                                  // 用户等级
  exp: "",                                    // 用户经验值
  chara: "",                                  // 用户角色
  optional: ""                                // 任意项目
);
```


|参数|内容|必须|备注|
|:------|:------|:------|:------|
|第１个参数|pageID|可选|联络本公司发行。<br/>※ iOS和Android通用|
|第２个参数|sceneID|可选|联络本公司发行。<br/>多个sceneID的情况下、用" &#124; "间隔（最大20个）<br/>不指定sceneID的情况下，将隐藏攻略浮动图标<br/>※ iOS和Android通用|
|第３个参数|用户等级|可选|以用户等级过滤攻略。<br/>（不设定等情况下，传入空字符串）|
|第４个参数|用户经验值|可选|以用户经验值过滤攻略。<br/>（不设定等情况下，传入空字符串）|
|第５个参数|用户角色|可选|以用户角色过滤攻略。<br/>（不设定等情况下，传入空字符串）|
|第６个参数|任意項目|可选|以json格式传入任意项目。 <br/>（不设定等情况下，传入空字符串）|

【关于任意项目】  
设定任意项目请参考下面条款。  
下面的表格以外的项目可以以任意项目的形式指定。如果希望追加项目，可联系本公司商谈。  
任意项目里面包含多字节字符的情况下，脚本的编码格式需要设定为UTF-8（带BOM）。

|任意項目名|key|设定值|
|:------|:------|:------|
|用户ID|puid   |游戏中的用户ID|
|用户名|pname  |游戏中的用户名|
|boss名|bchar  |游戏中的boss名|
|等级(排名)|level  |用户等级或者等级称为之类|
|经验值|exp  |用户经验值或者称为|
|角色名|char  |用户游戏角色名称|
|HP|hp  |游戏人物HP|
|体力|stm  |游戏人物体力值|
|攻击力|power  |游戏人物攻击力|
|装备/武器名|item  |游戏人物武器、装备名称|

※各項目最大输入128字。  

<a name="sample"></a>
### 4-3. 整体流程（SDK功能使用）

1. App启动
2. 按照「4-1.App启动」设定ID
3. 户开始玩游戏
4. 「4-2. 显示游戏攻略的浮动图标」
5. 用户点击游戏攻略浮动图标
6. 显示游戏攻略

## 5. SDK导入后测试

### 5-1. 确认事项

申请上线前，请测试导入的SDK的状态，以及各功能是否可以正常运行。

1. 用测试机启动导入SDK的App
1. 到 **游戏攻略** 画面
1. 显示 **浮动图标**
1. 点击 **浮动图标** 后显示游戏攻略
1. 关闭 **攻略情報** 画面


通知本公司贵社的测试日期。我们将通过肩擦日志文件确认SDK的功能有没有正常被调用。  
本公司确认之后，没有问题后，测试结束。


### 5-2. 关于调试模式

我们提供了测试时确认用的调试模式

【调试模式功能】

* 不执行过滤的情况下、接收测试网页以及测试视频。  
* 把开发环境的标准输出写入日志。

|调试模式scene|sceneID|
|:------|:------|
|scene1|fc7f24abc577f37d44b6fb0095dcd976|
|scene2|aee4857751950eee5441057d436e506a|
|scene3|ea0a160b2a496c33efe0c9511f61b34f|
|scene4|c0f7d3256b91dc80faab2d0ca34696e6|
|scene5|a3c9cc0625a56a2f9abb1400f2e17f80|
|scene6|46d8ca247c7cdd3f81c0649b1bc3c2fc|
|scene7|f576372b68c175cb52730f05a510eb86|
|scene8|3ae0fa5850a7ef180b56617f271124a7|
|scene9|a353d25ddc95549b6bb848fbf6401510|
|scene10|a63b500d519653e2306c77213ef91eb9|

测试结束后，本社将解除SDK的调试模式，变成运营模式。

----

导入SDK的时候，如果有什么问题，请发送邮件到下面的地址咨询。

咨询邮箱：z-mediadev@cyber-z.co.jp

