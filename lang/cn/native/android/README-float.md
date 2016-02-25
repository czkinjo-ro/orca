# 大家的游戏攻略 Android SDK 导入手册

### 1-1. 运行环境
 * Android 4.2.2 以上

### 1-2. 术语
 * **"scene"**  
这个项目中游戏攻略的各个任务，关卡、活动等统称为“scene”。

* **"page"**  
贵公司游戏中打开的攻略画面被称为"page"。

## 2. 下载SDK

正在准备

## 3. 导入到项目

* [Eclipse项目导入方法](/lang/cn/doc/integration/eclipse)

## 依赖库

贵公司的App如果没有使用下面的库，需要另外导入。

|名称|導入手順|
|:--|:--|
|Google Play Services|[官网](https://developers.google.com/android/guides/setup)  （不使用AdvertisingId的情况下非必要）|
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
### * 使用Google Play Services请款下的配置
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


使用ProGuard对程序进行代码混淆的时候，需要禁止对 ORCA SDK 的方法做代码混淆，需要追加下面的配置。


```prolog
-keepattributes *Annotation*

-libraryjars libs/orca-androidsdk.jar
-keep class net.orcaz.sdk.** { *; }
```

另外，导入GooglePlayServiceSDK的情况下，参考下面的页面配置keep值。

[Google Play Services导入时使用Proguard](https://developer.android.com/google/play-services/setup.html#Proguard)


## 4. SDK功能使用

通过用C#脚本调用定义好的函数名来使用SDK的功能。

### 4-1. App启动

**程序启动时** 请设置clientID和applicationID。  
ID的发行请联系本社。

[导入实例]

```java

import net.orcaz.sdk.Orca;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    Orca.configure(
      this,                               // activity
      "86b8094d44c175850b18ec31eb9adf71", // clientID
      "d1d1b00e741d2417a4a1f55b1eda5bed"  // applicationID
    );
}
```

|参数|内容|必须|备注|
|:------|:------|:------|:------|
|第1个参数|activity|必须|Application的activity|
|第2个参数|clientID|必須|联系本社发行。<br/>|
|第3个参数|applicationID|必須|联系本社发行。<br/>※ iOS和Android通用|

### 4-2. 显示游戏攻略的浮动图标

想在贵公司的游戏画面上显示**游戏攻略的浮动图标**时，执行以下步骤。  
pageID、sceneID请联系本社发行。  
pageID可以设置也可以不设置，不设置的情况下不提供下面的功能。  
・统计每个page的攻略访问次数  
・每个page的浮动图标变更  

[导入实例]

```java
Orca.showFloatIcon(
  this,                               // acitivity
  "d4dc9737ecb40d25d1bc5b8a1f7798d4", // pageID
  "65928b3ceeb3e9cb24d917e5532ad332|afc4a607d40edbe7c51853ac3af0910f",  // sceneID（有多个攻略的情况）
  "レベル1",                           // 用户等级
  "15",                                // 用户经验值
  "勇者",                              // 用户角色
  "{'OptionalData':{'puid':'1234','pname':'山田太郎','level':'レベル1', ・・・ }}" // 任意项目
);
```

[导入实例（不设置过滤条件的情况）]

```java
Orca.showFloatIcon(
  this,                                // acitivity
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",  // pageID
  "65928b3ceeb3e9cb24d917e5532ad332",  // sceneID（攻略情報が1つだけの場合の例）
  "",                                  // 用户等级
  "",                                  // 用户经验值
  "",                                  // 用户角色
  ""                                   // 任意项目
);
```

#### 攻略浮动图标变更

游戏画面切换时，调用函数并指定pageID、sceneID，参考「4-2. 显示游戏攻略的浮动图标」。
已经显示在画面上的图标会被删除，显示新的浮动图标。

#### 自定义攻略浮动图标

准备好每个page图标的设计、尺寸、图片，在本公司的系统页面上传。

#### 隐藏示攻略浮动图标

想隐藏已经显示在画面上的攻略浮动图标的话，不指定scenceID再执行「4-2. 显示游戏攻略的浮动图标」的步骤。


[导入案例（隐藏浮动图标）]

```java
Orca.showFloatIcon(
  this,                                // acitivity
  "d4dc9737ecb40d25d1bc5b8a1f7798d4",  // pageID
  "",                                  // sceneID（有多个攻略的情况））
  "",                                  // 用户等级
  "",                                  // 用户经验值
  "",                                  // 用户角色
  ""                                   // 任意项目
);
```

|参数|内容|必须|备注|
|:------|:------|:------|:------|
|第1个参数|activity|必须|Application的activity|
|第2个参数|pageID|可选|联络本公司发行。<br/>※ iOS和Android通用|
|第3个参数|sceneID|可选|联络本公司发行。<br/>多个sceneID的情况下、用" &#124; "间隔（最大20个）<br/>不指定sceneID的情况下，将隐藏攻略浮动图标<br/>※ iOS和Android通用|
|第4个参数|用户等级|可选|以用户等级过滤攻略。<br/>（不设定等情况下，传入空字符串）|
|第5个参数|用户经验值|可选|以用户经验值过滤攻略。<br/>（不设定等情况下，传入空字符串）|
|第6个参数|用户角色|可选|以用户角色过滤攻略。<br/>（不设定等情况下，传入空字符串）|
|第7个参数|任意項目|可选|以json格式传入任意项目。 <br/>（不设定等情况下，传入空字符串）|

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

### 4-3. App后台运行和重新转入前台运行

App后台运行和重新转入前台运行时，控制画面上攻略浮动图标的显示。

后台运行后转入前台运行的时候，需要重新调用SDK函数。

[导入实例]

```java
@Override
public void onResume() {
    Orca.getInstance().resume();
}

@Override
public void onPause() {
    Orca.getInstance().pause();
}
```

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
