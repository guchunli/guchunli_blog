---
title: iOS调试与性能优化学习笔记
date: 2017-06-05 18:31:25
categories: 笔记
tags: [iOS,优化]
---

1.全局断点
2.条件断点 condational
3.符号断点 symbolic  -[NSObject(NSObject) doesNotRecognizeSelector:]
4.NSLog
5.僵尸对象
<!--more-->
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
9.预编译指令:
//swift语言
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

