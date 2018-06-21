---
title: iOS国际化学习笔记
date: 2016-12-19 10:30:38
categories: 笔记
tags: [iOS,国际化]
toc: true
---

## 配置需要国际化的语言
project->Info->Localizations 添加本地化语言包
<!--more-->
## App名称本地化
(1)创建`InfoPlist.string`文件，Xcode的`File inspection`中`Localize`选择需要本地化的语言
(2)在不同语言InfoPlist.strings对应文件中添加应用名称
* InfoPlist.strings(English)
```
//App在英语环境下显示的名称
CFBundleDisplayName = "AppName";
```

* InfoPlist.strings(Chinese)
CFBundleDisplayName = "应用名称";

(3)info.plist添加`Application has localized display name`，值为YES

## 代码中字符串本地化
(1)创建`Localizable.strings`文件，Xcode的`File inspection`中`Localize`选择需要本地化的语言
(2)以KeyValue的形式，为代码中每一个需要本地化的字符串赋值
* Localizable.strings(English)
```
"hello" = "Hello!";
```

* Localizable.strings(Chinese)
```
"hello" = "你好";
```

(3)使用Foundation框架自带的NSLocalizedString(key, comment)这个宏根据Key获取对应字符串
NSlocalizeString 第一个参数key是内容,根据key去对应语言的文件中获取对应的字符串，如果没有找到，那么就会以key作为value返回，第二个参数将会转化为字符串文件里的注释。
#define NSLocalizedString(key, comment) [[NSBundle mainBundle] localizedStringForKey:(key) value:@"" table:nil]

## 多人开发情况下的字符串本地化
不在系统默认的本地化文件`Localizable.strings`中定义字符串，自定义strings文件储存本地化字符串，然后通过`NSLocalizedStringFromTable(\<#key#\>, <#tbl#>, <#comment#>)`获取。
* tbl：自定义strings文件的名称
## 图片本地化（两种方式两种方式）
第一张方式：同获取本地化字符串方式获取图片名称进行加载
第二种方式：将图片本地化，选中图片，在Xcode的`File inspection`中`Localize`选择需要本地化的语言，项目目录中显示`service(English)`和`service(Chinese)`两张图片，分别替换为不同语言的两张图片，实际是存储在`en.lproj`和`zh-Hans.lproj`中同为`service.png`名称的两张图片。
两种方式同获取本地化字符串相同获取图片名称：
```
NSString *imgName = NSLocalizedString(@"service", nil);
```

## 查看/切换本地语言
查看本地语言环境：从NSUserDefaults中获取key为`AppleLanguages`的value即为当前语言环境。
```
NSArray *languages = [[NSUserDefaults standardUserDefaults] valueForKey:@"AppleLanguages"];
NSString *currentLanguage = languages.firstObject;
```

切换当前语言环境：
```
NSArray *lans = @[@"zh-Hans"];
[[NSUserDefaults standardUserDefaults] setObject:lans forKey:@"AppleLanguages"];
```

> 也可通过Edit Scheme->Run->Arguments Passed On Launch ->-AppleLanguages (语言)改变语言环境

原文链接：[3分钟实现iOS语言本地化/国际化（图文详解）](http://www.jianshu.com/p/88c1b65e3ddb)

