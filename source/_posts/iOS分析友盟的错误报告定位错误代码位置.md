---
title: iOS分析友盟的错误报告定位错误代码位置
date: 2017-11-06 18:19:13
categories: 学习
tags: [友盟,崩溃,错误]
toc: true
---

## 命令行方式
### 1.在Xcode的Windows->Orgnizer->Archives可以看到每次的打包，找到要查看的错误报告所在的版本。
 * /Users/<用户名>/Library/Developer/Xcode/Archives 路径下是所有打包文件
 
<!--more-->
### 2.show in Finder，查看包内容，将`.dSYM`文件和`.app`复制保存到一个新的文件夹crash。

### 3.使用终端
```
cd crash
xcrun atos -arch arm64 -o XXX.app/XXX 0x1000e7068
```

* `arm64`为CPU Type
*  `XXX`为项目名称
* `0x1000e7068`为错误代码的地址

使用以上命令即可得到错误代码所在的文件以及出错代码的具体行数。

## 使用工具软件
下载地址：[dSYMTools](https://github.com/answer-huang/dSYMTools)

* 如何将文件一一对应：xxx.app 、 xxx.app.dSYM 和crash 文件都有自己的 UUID，只要这三个文件的 UUID 一致，就可以解析出正确的错误函数信息。

1.查看 xx.app 文件的 UUID：
```
dwarfdump --uuid xxx.app/xxx (xxx代表项目名)
```

2.查看 xx.app.dSYM 文件的 UUID：
```
dwarfdump --uuid xxx.app.dSYM
```

3.crash 文件内第一行 Incident Identifier 就是 crash 文件的 UUID。

## 使用xcode自带的工具symbolicatecrash

参考文章：[如何通过友盟的错误分析报告，定位到错误代码的位置](http://www.jianshu.com/p/17fd659df460?open_source=weibo_search)

[dSYM 文件分析工具](http://www.cocoachina.com/ios/20141219/10694.html)

