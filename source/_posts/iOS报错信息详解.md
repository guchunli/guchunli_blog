---
title: iOS报错信息详解
date: 2017-11-06 11:46:41
categories: 学习
tags: [bug,崩溃,报错]
toc:
---

## 链接问题，有重名的文件
```
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

<!--more-->
## 数组未初始化
```
invalid mode 'kCFRunLoopCommonModes' provided to CFRunLoopRunSpecific - break on _CFRunLoopError_RunCalledWithInvalidMode to debug. This message will only appear once per execution.
```

## 缺少文件
```
"_OBJC_CLASS_$_XXX", referenced from:
```
原因：缺少文件，或文件未参与编译，framework 没导入，真机和测试也要注意，.m文件为参与编译

解决：在build Phases->Compile Sources 中添加相应的类文件

## 待补充
