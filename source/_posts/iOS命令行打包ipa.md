---
title: iOS命令行打包ipa
date: 2018-06-04 14:20:09
categories: 学习
tags: [archive,打包,ipa]
toc: true
---

## 打包步骤
### clean

使用cocoapods：
`xcodebuild clean -workspace ${TARGET_NAME}.xcworkspace -scheme ${TARGET_NAME} -configuration ${BUILD_TYPE}`
* `-workspace`： 如果项目中没有使用到CocoaPods，则该命令可以不用。
* `-scheme`： 和工程名字一样，
没有使用cocoapods：
`xcodebuild clean -project ${TARGET_NAME}.xcodeproj -configuration ${CONFIGURATION} -alltargets`

### archive：生成build文件夹
1.
`xcodebuild -target APPNAME -configuration Release`

2.
`xcodebuild archive -workspace ${TARGET_NAME}.xcworkspace -scheme ${TARGET_NAME} -archivePath {ARCHIVEPATH}`
* `-archivePath`：后面跟的是编译后生成的archive包的路径。

`xcodebuild archive -project /Users/xxx/Desktop/APPNAME/APPNAME.xcodeproj -scheme APPNAME -archivePath bin/APPNAME.xcarchive`
* `/Users/xxx/Desktop/APPNAME/APPNAME.xcodeproj`为工程路径
* `bin/APPNAME.xcarchive`为.xcarchive文件的目标路径

### export

`xcodebuild -exportArchive -archivePath "${ARCHIVEPATH}/${TARGET_NAME}.xcarchive" -exportPath ${EXPORTPATH} -exportOptionsPlist ${EXPORTOPTIONSPLIST}‘`

*  `${TARGET_NAME}` 项目对应targets的名字
* `${BUILD_TYPE}` 打包类型 Debug，Release 等
* `${archivePath}` .xcarchive文件导出目录
* `${EXPORTPATH}` 导出.ipa包的目录
* `${EXPORTOPTIONSPLIST}` exportOptionsPlist文件所在目录，可判断development, ad-hoc等
1.根据配置文件名称导出：
`xcodebuild -exportArchive -archivePath bin/APPNAME.xcarchive -exportPath APPNAME -exportFormat ipa -exportProvisioningProfile "yourProvisioningProfileName"`
* `yourProvisioningProfileName`为配置文件（Provisioning Profile）的名称
2.直接导出
`xcrun -sdk iphoneos  PackageApplication -v /Users/xxx/Desktop/APPNAME/build/Release-iphoneos/APPNAME.app -o /Users/xxx/Desktop/APPNAME/APPNAME.ipa`
* `/Users/xxx/Desktop/APPNAME/APPNAME.ipa`为导出ipa的目标路径

## 实例
```
xcodebuild clean -workspace ScalperMerchant.xcworkspace -scheme ScalperMerchant -configuration Debug

xcodebuild archive -workspace ScalperMerchant.xcworkspace -scheme ScalperMerchant -archivePath build/ScalperMerchant.xcarchive archive

xcodebuild -exportArchive -archivePath build/ScalperMerchant.xcarchive -exportPath build -exportOptionsPlist ExportOptions.plist
```

## 如果要打 release 的包
1.打包类型改为 release
2.在ExportOptions.plist 修改 method  为 app-store 
method 包含四种： app-store, ad-hoc, enterprise, development
3.provisioningProfiles   修改为  上线配置文件    
4.signingCertificate   修改为上线证书
```
xcodebuild clean -workspace ScalperMerchant.xcworkspace -scheme ScalperMerchant -configuration  Release
```


报错：`xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance`
问题：Xcode的路径被改了。
解决：重置Xcode路径
`sudo xcode-select -switch /Applications/Xcode8.2.1.app/Contents/Developer`

参考文件：[iOS 命令打包](https://blog.csdn.net/qq_31942467/article/details/79665053)

1.xcodebuild clean
2.xcodebuild -workspace BlockCar.xcworkspace  -scheme BlockCar -archivePath build/BlockCar.xcarchive archive



