# iOS项目配置

## package结构

* iOS项目/
  * Classes/
    * `CCOrca.h`
    * `CCOrca.cpp`
  * proj.ios/
    * libs
      * `OrcaSDK.framework`

请把Package内的文件导入到对象项目内。
  * 请拷贝`CCOrca.h`と`CCOrca.cpp`到Classes下面。
  * 请拷贝`OrcaSDK.framework`到proj.ios/libs下面。

* [Framework的添加方法](/lang/cn/native/ios/xcode/README.md)
* [App Transport Security例外配置](/lang/cn/doc/ats)
