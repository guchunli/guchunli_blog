---
title: iOS调试与优化技巧
date: 2017-06-05 18:31:25
categories: 笔记
tags: [iOS,优化,待补充]
---

# 调试
## 断点
### 全局断点（Global BreakPoint）

### 异常断点（Exception Breakpoint）
项目crash时，添加异常断点运行，会定位到出错的代码行

### 条件断点（Condational Breakpoint）
右击断点，condition中添加触发断点的条件，比如循环中使用

###  符号断点（Symbolic Breakpoint）
* Symbol中输入 viewDidLoad，则在程序中所有的 viewDidLoad 方法被调用时都会触发断点。
* 我们也可以仅仅为特定的某个类的方法添加断点。在 Symbol 一栏输入 [ClassName viewDidLoad] (Objective-C) 或 ClassName.viewDidLoad (Swift) 即可。
* unrecognized selector sent to instance 0xaxxxx 这种错误，这个instance可以这样快速定位：-[NSObject(NSObject) doesNotRecognizeSelector:]

## 打印日志 NSLog
NSLog的打印是非常低效的，甚至比print低100倍，以下NSLog更高效，并且可以获取更多信息。
```
//A better version of NSLog
#define NSLog(format, ...) do { \
fprintf(stderr, "<%s : %d> %s\n", \
[[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], \
__LINE__, __func__); \
(NSLog)((format), ##__VA_ARGS__); \
fprintf(stderr, "-------\n"); \
} while (0)
```
`使用do-while是为了避免吃掉分号问题`

*  不知道%@，%d的时候可以使用全局方法
```
NSStringFromClass
NSStringFromCGRect
...
```

## 僵尸对象
开启：edit scheme->Diagnostics->Zombie Objects，当访问已经被释放的对象时将会获取到更多的信息。

## LLVM lldb
LLVM：编译器
lldb：LLVM的调试器

### expression
可以在调试时动态执行指定表达式，并将结果打印出来。常用于在调试过程中修改变量的值。
expression的完整语法是这样的：
```
expression <cmd-options> -- <expr></expr>
```
### p & print & call
一般情况下，我们直接用expression还是用得比较少的，更多时候我们用的是p、print、call。这三个命令其实都是expression –的别名（–表示不再接受命令选项，详情见前面原始(raw）命令这一节）
1. print: 打印某个东西，可以是变量和表达式
2. p: 可以看做是print的简写
3. call: 调用某个方法。一般只在不需要显示输出，或是方法无返回值时使用

### po
一般打印的时候，打印出来的是对象的指针，而不是对象本身。如果我们想打印对象。我们需要使用命令选项:-O。为了更方便的使用，LLDB为expression -O –定义了一个别名：po

### frame（帧）
输入命令`bt`，可以打印出来所有的frame。如果仔细观察，这些frame和左边红框里的堆栈是一致的。平时我们看到的左边的堆栈就是frame。

### c & n & s & finish
1. c/ continue/ thread continue: 这三个命令效果都等同于上图中第一个按钮的。表示程序继续运行
2. n/ next/ thread step-over: 这三个命令效果等同于上图第二个按钮。表示单步运行
3. s/ step/ thread step-in: 这三个命令效果等同于上图第三个按钮。表示进入某个方法
4. finish/ step-out: 这两个命令效果等同于第四个按钮。表示直接走完当前方法，返回到上层frame

### image
image 命令可用于寻址，有多个组合命令。比较实用的用法是用于寻找栈地址对应的代码位置。
* image lookup --address
当发生崩溃时，想寻找栈地址对应的代码位置，简写为image lookup -a。
```
image lookup --address 0x0000000100004af8
```

* image lookup --name
当我们想查找一个方法或者符号的信息，比如所在文件位置等，尤其当文件被封装为.a库时，简写为image lookup -n。
```
(lldb) image lookup -n dictionaryWithXMLString:
```

* image lookup –type
当我们想查看一个类型的时候，可以使用image lookup --type，简写为image lookup -t
```
(lldb) image lookup -t Model
```

## 预编译指令
### 文件包含
#### #include
#include <>：预处理器会搜索C函数库头文件路径下的文件
#include ""：首先搜索程序所在目录，其次搜索系统Path定义目录，如果还是找不到才会搜索C函数库头文件所在目录。
* 使用#include的时候包含文件的时候是不能递归包含，重复包含。替代：1、使用#import替代include 2、使用宏判断
#### #import
OC特有的就是一个智能的#include，解决了#include的重复包含的问题。

### 宏定义
C中的宏分为两类，对象宏(object-like macro)和函数宏(function-like macro)。
可以使用`\`将宏分成了好几行来写。
#### #define 
#### #undef
当你使用了#define宏定义后，则在整个程序的运行周期内这个宏都是有效的，但有时候我们在某个逻辑里希望这个宏失效不想使用，则会使用：
```
#define NetworkOn //定义一个宏，如果该宏定义了，则在应用里使用网络
-(void)closeNetwork{//突然发生意外的情况，网络无法使用了，调用该方法，取消NetworkOn的宏定义
#undef NetworkOn
}
```

### 条件编译
#### #if #else #endif
判断是否开启ARC
```
#if !__has_feature(objc_arc)
//如果没有开启ARC这里可以做一些错误处理
#error "没有开启ARC"
#endif
```

判断系统版本
```
#if __IPHONE_OS_VERSION_MIN_REQUIRED < __IPHONE_7_0
//如果iOS版本低于7.0
#endif
```

判断设备信息
```
#define IS_IPAD (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)
#if IS_IPAD
//这台设备是IPAD
#else
//不是
#endif
```

#### #if define #ifdef #ifndef #elif
#if define = #ifdef
#if !define = #ifndef
#elif = "else if"

### 错误、警告处理
#### #error
#### #warning
Build Settings->Treat Warning as Errors，如果你设置成Yes，那么你的waring就等于error，编译不了。
* 创建一个警告：
```
//Generate a warning
#pragma message "Warning 1"

//Another way to generate a warning
#warning "Warning 2"
```

* 关闭警告
如果需要全局关闭的话，直接在Other C Flags里写-Wno-...就行了，比如-Wextra -Wno-sign-compare就是一个常见的组合。
如果相对某几个文件开启或禁用警告，在Build Phases的Compile Source相应的文件中加入对应的编译标识即可。
如果只是想在某几行关闭某个警告的话，可以通过临时改变诊断编译标记来抑制指定类型的警告，具体如下：
```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wunused-variable"

int a;

#pragma clang diagnostic pop
```

### 编译器控制
#### #pragma
#pragma mark
#pragma mark -
```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "clang的参数"
#pragma clang diagnostic pop
```

### 其他
### #line
改变当前行的行号在编译器中的表示
```
#line 100  //指定下一行的__LINE__为100
printf("%d\n",__LINE__);
```

# 优化
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
leaks:自动加载Allocations，监控程序运行过程中的内存变化
### 3.僵尸对象 Zoombies

### 4.被遗弃的内存 Generational Analysis

### 5.Allocations

### 6.Energy Log


学习资料：
[《How to Use Instruments in Xcode》](https://www.raywenderlich.com/97886/instruments-tutorial-with-swift-getting-started)
[《与调试器共舞 - LLDB 的华尔兹》](https://objccn.io/issue-19-2/)
[《LLDB调试命令初探》](http://www.starfelix.com/blog/2014/03/17/lldbdiao-shi-ming-ling-chu-tan/)
[《The LLDB Debugger》](http://lldb.llvm.org/tutorial.html)
[《About LLDB and Xcode》](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/gdb_to_lldb_transition_guide/document/Introduction.html)
[浅谈LLDB调试器](https://www.cnblogs.com/lxlx1798/p/6652821.html)
