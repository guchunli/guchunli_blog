---
title: Carthage的安装与使用
date: 2018-02-26 15:53:36
categories: 笔记
tags: [Carthage]
toc:
---

## Carthage的安装与使用
### Carthage的安装
```
$ brew update
Error: /usr/local is not writable. You should change the
ownership and permissions of /usr/local back to your
user account:
sudo chown -R $(whoami) /usr/local
$ sudo chown -R $(whoami) /usr/local
$ brew install carthage
```
<!--more-->
### 创建Cartfile文件
```
vim Cartfile
```

### 在Cartfile文件中引入一个第三方库
```
//比如引入SwiftyJSON
github "SwiftyJSON/SwiftyJSON"
```

### 安装第三方库
```
carthage update
```

构建完成后，在项目的根目录中自动生成一个Carthage目录。这个目录中又包含了两个目录Build和Checkout。 `Build`中存放的是构建好的framework包，`Checkouts`中存放的是签出的第三方库项目源文件。

### 添加framework
Carthage 只支持动态框架，它仅负责将项目 clone 到本地并将对应的 Cocoa Framework target 进行构建，之后你需要自行将构建好的 framework 添加到项目中，所有的引入库的源和版本需要自己管理。
添加framework：
general->Linked Frameworks and Libraries->add->add other->Carthage->Build->iOS->xxx.framework

## Carthage文件夹下各个文件的用途
* Cartfile文件：用来标注需要哪些依赖库，对应版本或者 Git 分支,当然前提必须提交到Git。
* Cartfile.resolved文件：用来跟踪项目当前所用的依赖版本号，主要为了保持多端开发一致。
* Carthage文件夹：用来存放依赖库的源文件和编译后的文件 (不需要提交到 Git)。

## Carthage与CocoaPods优缺点
* 优点：
    * Carthage更加灵活
    * 很多库不需要声明并改造就直接可以被 Carthage 用
* 缺点：
    * 不如 CocoaPods 丰富
    * 有大量 CocoaPods 能用的库不支持

参考链接：[走向Carthage](https://www.jianshu.com/p/3921289cd3c5)
[Swift Package Manager](https://swift.org/package-manager/)
