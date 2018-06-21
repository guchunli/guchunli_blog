---
title: iOS报错信息详解
date: 2017-11-06 11:46:41
categories: 笔记
tags: [bug,崩溃,报错]
toc:
---

## duplicate symbols for architecture x86_64

* 原因：1.在工程中不同路径下重复导入了某个类或文件；  2.引用了.m文件，#import "XXX.m"； 3.多个文件中重复定义了相同的枚举等

* 解决：1.查找，删除； 2.查找，修改； 3.查找，删除。 如果还是不能解决，试着删除该文件，重新添加。

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
