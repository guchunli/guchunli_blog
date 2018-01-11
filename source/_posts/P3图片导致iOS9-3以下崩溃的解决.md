---
title: P3图片导致iOS9.3以下崩溃的解决
date: 2017-12-12 13:28:39
categories: 学习
tags: [P3图片]
toc:
---

* 这种问题一般在Xcode上运行正常，真机测试运行正常，打包上传到蒲公英下载安装运行正常，只有上传到AppStore下载安装才会崩溃。
<!--more-->
* 导致这种问题的原因是：在 Xcode 8 中，当你资源文件中[含有16位图]或者[图片显示模式γ值为'P3']且iOS targets设定为iOS 9.3以下就会出现这个问题. 如果你的app需要支持广色域显示的话，那你必须得把target设置成iOS 9.3+，相反，如果你的app不需要支持广色域且你想兼容 iOS 9.3 之前的项目，你就得把所有的16位的或者显示模式为'P3'图片全都替换成8位模式的SRGB颜色的图片。

### 解决方案如下：
1.导出项目的ipa文件
2.修改.ipa后缀为.zip
3.解压.zip文件，会生成一个Payload文件夹，文件夹下包含项目的app bundle文件
4.打开终端，切换到app bundle文佳夹，定位到Assets.car文件，使用assetutil命令找到包含16-bit或P3的资源文件，将分析结果导出到json文件中，命令如下：
```
$ cd /path/Payload/XXX.app/
$ find . -name 'Assets.car'
./Assets.car
$ sudo xcrun --sdk iphoneos assetutil --info ./Assets.car > /tmp/Assets.json    //./Assets.ca为上一步查找的结果  /tmp/Assets.json为导出的json文件路径
$ open /tmp/Assets.json
```

5.在json文件中查找包含`"DisplayGamut" : "P3"`的相关内容，`"Name" : "picBroken"`为对应图片名称，修改替换对应图片文件。json文件格式如下：
```
{
"Opaque" : false,
"Compression" : "lzfse",
"SizeOnDisk" : 5968,
"PixelHeight" : 192,
"Name" : "picBroken",
"RenditionName" : "picBroken@3x.png",
"ColorModel" : "Monochrome",
"Scale" : 3,
"Idiom" : "universal",
"DisplayGamut" : "P3",
"Encoding" : "GRAY-16",
"BitsPerComponent" : 16,
"Image Type" : "kCoreThemeOnePartScale",
"Colorspace" : "extended gray",
"AssetType" : "Image",
"PixelWidth" : 192
},
```

补充：
* iOS Console：可以脱离xcode打印log，以及打印ios系统收集的crash 日志。
* BashShell：命令行打包

参考文章：[P3图片导致iOS9.3以下崩溃问题](http://www.sohu.com/a/145632913_487493)
[iOS线上版本图片资源格式的问题导致的闪退](http://www.jianshu.com/p/6492779cb89e)
