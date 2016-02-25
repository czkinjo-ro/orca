## AdSupportの導入(Unity)


### 1. Adsupport.frameworkの追加（Unity5以降）
1. Unityプロジェクトにて"OrcaSDK_for_iOS.framework"を選択する。
2. Inspectorの"Platform settings"の"Framework dependencies"内の"Adsupport"にチェックをつける。

![adsupport01](./img01.png)


### 2. Adsupport.frameworkの追加（Unity4以前）

1. Unityから出力されたXcodeのプロジェクトを開き、対象Targetの"Build Phasees"を表示。
2. "Linked Frameworks and Libraries"の+ボタンを押す。
3. "Adsupport.framework"を追加。

![adsupport02](./img02.png)
