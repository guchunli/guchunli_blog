---
title: iOS报错信息详解
date: 2017-11-06 11:46:41
categories: 学习
tags: [bug,崩溃,报错]
toc:
---

## clang: error: linker command failed with exit code 1 (use -v to see invocation)

* 原因：链接问题，有重名的文件

* 解决：查找，删除

<!--more-->
## invalid mode 'kCFRunLoopCommonModes' provided to CFRunLoopRunSpecific - break on _CFRunLoopError_RunCalledWithInvalidMode to debug. This message will only appear once per execution.

* 原因：数组未初始化

* 解决：初始化数组

## "_OBJC_CLASS_$_XXX", referenced from

* 原因：缺少文件，或文件未参与编译，framework 没导入，真机和测试也要注意，.m文件为参与编译

* 解决：在build Phases->Compile Sources 中添加相应的类文件

## index 0 beyond bounds for empty array

* 原因：数组越界，index 0 在空数组中也是越界，不存在。取到的值是NSNull  对象。 它表示空值，只有一个方法。调用NSArray的属性方法，程序会dump。
```
@interface NSNull : NSObject <NSCopying,NSSecureCoding>

+ (NSNull *)null;

@end
```

* 解决：判断array 不为空
```
if (array != nil && ![array isKindOfClass:[NSNull class]] && array.count != 0)
```

## 待补充
