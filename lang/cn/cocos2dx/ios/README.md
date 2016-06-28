# iOSプロジェクトの設定

## パッケージ構成

* iOSプロジェクト/
  * Classes/
    * `CCOrca.h`
    * `CCOrca.cpp`
  * proj.ios/
    * libs
      * `OrcaSDK.framework`

パッケージ内のファイルを対象プロジェクトに組み込んで下さい。
  * Classes配下に`CCOrca.h`と`CCOrca.cpp`をコピーしてください。
  * proj.ios/libs配下に`OrcaSDK.framework`をコピーしてください。

* [フレームワークの追加方法](/lang/ja/native/ios/xcode/README.md)
* [App Transport Security の例外設定](/lang/ja/doc/ats)
