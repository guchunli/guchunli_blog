---
title: iOS中手动忽略编译警告
copyright: true
date: 2019-08-15 14:50:11
categories: 笔记
tags:
toc: true
---

* 忽略警告
<!--more-->
```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "相关命令"
// 这是是出现警告的代码
#pragma clang diagnostic pop
```

常见的命令有 
1.方法废弃
```
#pragma clang diagnostic push  
#pragma clang diagnostic ignored "-Wdeprecated-declarations"      
//废弃的方法
#pragma clang diagnostic pop
```

2.不兼容指针类型
```
#pragma clang diagnostic push   
#pragma clang diagnostic ignored "-Wincompatible-pointer-types"  
//
#pragma clang diagnostic pop
```

3.未使用变量
```
#pragma clang diagnostic push     
#pragma clang diagnostic ignored "-Wunused-variable"     
int a;     
#pragma clang diagnostic pop   
```

4.
```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Warc-performSelector-leaks"
[observer performSelector:selector withObject:notification];
#pragma clang diagnostic pop
```

如果需要每个属性或每个方法都去指定nonnull和nullable，是一件非常繁琐的事。苹果为了减轻我们的工作量，专门提供了两个宏:
```
NS_ASSUME_NONNULL_BEGIN

NS_ASSUME_NONNULL_END
```
