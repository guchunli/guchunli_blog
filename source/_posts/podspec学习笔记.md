---
title: podspec学习笔记
date: 2017-07-11 13:56:12
categories: 笔记
tags: [podspec]
---

1.创建spec文件：
```
pod spec create spec文件名
```

2.创建工程
(1)将组件代码放到Classes中
(2)
```
pod lib create 组件代码名称
```
<!--more-->
需要重新`pod install`

3.验证spec文件
```
pod lib lint
pod lib lint --allow-warnings //屏蔽警告
pod lib lint --verbose  //获取错误的更多提示信息
```

出现`XXX passed validation.`信息则成功，否则按照提示进行修改。

4.指定tag
```
$ git tag -m "podspec description" "0.0.1"
$ git push --tags
```

5.推送到cocoapods官方库
```
//注册
pod trunk register EMAIL [NAME]
//推送
pod trunk push spec文件名
```

6.搜索
```
pod search spec文件名
```

7.安装
```
pod 'spec文件名'，'~>0.0.1' 
```

