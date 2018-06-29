---
title: iOS面试题总结
date: 2018-06-25 12:17:26
categories: 笔记
tags: [面试]
toc: 
---

### iOS平台怎么做数据的持久化?coredata和sqlite有无必然联系?coredata是一个关系型数据库吗?
<!--more-->
答：core data是对sqlite的封装，sqlite是c语言的api，core data把c的api翻译成oc的api，coredata还提供了一些管理的功能，使用更加方便。它提供了对象-关系映射(ORM)的功能，即能够将OC对象转化成数据，保存在SQLite数据库文件中，也能够将保存在数据库中的数据还原成OC对象。

### Object-c的类可以多重继承么?可以实现多个接口么?category是什么?重写一个类的方式用继承好还是分类好?为什么?
答: Object-c的类不可以多重继承；一般情况用分类好，用Category去重写类的方法，仅对本Category有效，不会影响到其他类与原有类的关系。

## #import跟#include有什么区别,@class呢?#import<>跟#import””有什么区别?
答: #import是Objective-C导入头文件的关键字
#include是C/C++导入头文件的关键字
使用#import头文件会自动只导入一次，不会重复导入，相当于#include和#pragma once；
@class告诉编译器某个类的声明，当执行时，才去查看类的实现文件，可以解决头文件的相互包含；
#import<>用来包含系统的头文件，#import””用来包含用户头文件。

### 对于语句NSString *obj =[[NSData alloc] init]; obj在编译时和运行时分别是什么类型的对象?
答:编译时是NSString的类型；运行时是NSData类型的对象

### 工厂模式

