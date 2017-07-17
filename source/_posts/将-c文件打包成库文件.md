---
title: 将.c文件打包成库文件
date: 2017-03-06 18:43:09
categories: 笔记
tags: [.c,.a,.so]
toc: true
---

## 打包.a

1.Cocoa Touch Static Library
2.添加要打包的文件:.c，.h
3.将头文件添加到Build Phases -> Copy Files中去
<!--more-->
4.需要修改Build Settings中的 Build Active Architecture Only 以满足运行不同CPU环境的模拟器。 将此设置为NO。
　选择Edit Scheme--> Buid Configuration设置为release
5.分别选择模拟器和真机编译，生成.a文件，在不同编译环境下编译会生成四种静态库，debug模拟器，debug真机，release模拟器，release真机
6.查看静态库所支持的CPU环境：lipo -info 静态库文件
7.合并静态库：lipo -create 静态库1 静态库2 -output 新静态库名称.a

注意：
1.如果直接拖拽的是一个项目，并将项目当做一个静态库引用需要这样处理：在 Build Phases 中的 Target Dependencies 和 Link Binary With Libraries 中分别添加项目。
如果仅仅是一个静态库的话，那么Link Binary WithLibraries 是必须添加的。
2.如果编译的静态库中有分类的话必须在 Build Settings --> Other Linker Flags 中加 -Objc 如果还崩溃，还得加上 -all_load

## 打包.so
1.编译
```
gcc -c -fPIC -o myfile.o myfile.c
```

2.生成.so文件
```
gcc -shared -o libname.so myfile.o
```

