---
title: iOS调试与性能优化学习笔记
date: 2017-06-05 18:31:25
categories: 笔记
tags: [iOS,优化,待补充]
---

## Analyze
* 静态内存分析：指在程序未运行时，通过工具对代码直接进行分析，根据代码的上下文语法结构，让编译器分析内存情况，检查是否有内存泄露。
### Analyze主要分析四种问题
* 1.逻辑错误：访问空指针或未初始化的变量等，建议在声明变量时，同时进行初始化。（len is a garbage value。）
* 2.内存管理错误：如内存泄露等（Potential leak of an object stored into 'XXX'。）
* 3.声明错误：创建了对象但未使用（Unused、Never read....）
* 4.API调用错误：未包含使用的库或框架

## Instruments
### 1.Time Profile
1.1 在底部的Call Tree中勾选：
<!--more-->
* Seperate by state：
* Separate by Thread（建议选择）：按照线程分类查看结果
* Invert Call Tree（不建议选择）：反向输出调用树，把调用层级最深的方法显示在最上面，如果想要查看哪个方法调用为最深时使用。
* Hide system Libraries（建议选择）：隐藏系统函数，选上后只会展示与应用有关的符号信息。
* Flatten Recursion（一般不选）：拼合递归，把同一递归函数产生的多条堆栈合并为一条
* Top Functions（可选）：选上后会将最耗时的函数降序排列，比如A调用了B，A的耗时时间是会包含B的耗时。

1.2 配置项目
scheme->profile->Build Configuration：Debug
project->debug information format->debug：DWARF with dSYM File

1.3 运行项目
在time profiler中，选择对应的device和process，点击红色按钮运行APP。
* 可以拖动时间轴查看其中一段时间的CPU使用情况
* 可以点击weight，按照时间消耗进行排序
选择真机运行APP，模拟器使用的是电脑的CPU

### 2.内存泄露 Leaks

### 3.僵尸对象 Zoombies

### 4.被遗弃的内存 Generational Analysis

### 5.Allocations

### 6.Energy Log

# 断点
## Exception Breakpoint
1.异常断点：项目crash时，添加异常断点运行，会定位到出错的代码行

2.条件断点 condational

3.符号断点 symbolic  -[NSObject(NSObject) doesNotRecognizeSelector:]
4.NSLog
5.僵尸对象
6.lldb LLVM GCC:p po expr call
image lookup --address 0x0000000100004af8
7.instruments-profile:
analyze：发现release问题，以及继承过程中的父类方法缺失等
leaks:自动加载Allocations，监控程序运行过程中的内存变化
《How to Use Instruments in Xcode》： https://www.raywenderlich.com/97886/instruments-tutorial-with-swift-getting-started
《与调试器共舞 - LLDB 的华尔兹》：https://objccn.io/issue-19-2/
《LLDB调试命令初探》：http://www.starfelix.com/blog/2014/03/17/lldbdiao-shi-ming-ling-chu-tan/
《The LLDB Debugger》：http://lldb.llvm.org/tutorial.html
《About LLDB and Xcode》：https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/Introduction.html
http://www.cnblogs.com/daiweilai/p/4234336.html
8.Debug View Hierarchy
9.预编译指令
```
//swift
#if DEBUG
func dlog<T>(object: T) {
println(object)
}
#else
func dlog<T>(object: T) {}
#endif

#warning
#error
#pragma message
```

