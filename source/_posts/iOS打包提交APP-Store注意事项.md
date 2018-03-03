---
title: iOS打包提交APP Store注意事项
date: 2017-04-13 12:20:22
categories: 学习
tags: [iOS,app store]
---

1.改为正式环境
* 接口改成线上服务器
* 极光推送环境改为生产环境等

<!--more-->
2.版本号
target -> general：`Version`和`Build`版本号

3.签名
target -> general -> signing

4.删除断点，log

5.本地化，语言包

6.Capabilities -> background modes 
如果后台使用了位置，提交信息中应用描述添加免责声明
> 温馨提示：GPS在后台持续运行，会大大降低电池的寿命。

7.设备选择`Generic iOS Device`，注意不要选择真机或模拟器。

8.edit scheme: run,archive -> `debug` 改为 `release`

9.打包ad-hoc，进行内测：clean -> build -> archive

最后，打正式包，提交APP Store。


## iOS的各种结果文件：
（1）app
iOS编译以后生成的原始文件，实际是一个文件夹，里面包含各种资源文件（图片，第三方bundle，plist等文件），程序的可执行文件(二进制格式)以及对所有文件的签名记录（_CodeSignature）
不能上传AppStore

（2）dSYM
生成.app时的附属产物。本质是一个文件夹，其中只有一个最大的文件，作用是对iOS程序闪退后产生的log文件进行符号化（desymbolicate）；通俗的说，就是把无意义的内存地址变成可读的程序中的类和方法以及代码行数
不能上传AppStore

（3）ipa
实际上就是把.app放到Payload文件夹后，对Payload进行了zip操作，最后改了下扩展名。
可通过Application Loader上传AppStore

（4）xcarchive
实际上也是一个文件夹，包含.ipa和.dSYM文件
可通过Xcode上传AppStore

[BuildSettings所有选项的官方说明](https://developer.apple.com/legacy/library/documentation/DeveloperTools/Reference/XcodeBuildSettingRef/0-Introduction/introduction.html)
