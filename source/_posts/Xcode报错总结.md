---
title: Xcode报错总结
date: 2018-01-11 10:22:34
categories: 笔记
tags: 
toc:
---

## Library not found for -xxx
Library Search Paths：没有配置或没有正确配置 .a静态库 导致报错

## xxx.h file not found
framework search paths ：项目中的framework静态库的路径配置错误或者没有配置
<!--more-->

## library not found for -lPods
<!--原来Apple不允许build包含swift静态库了。而cocoapods使用了frameworks方式来集成swift库，就是use_frameworks!这句话了-->

## Multiple commands produce 
1.Info.plist
在 target -> Build phase > Copy Bundle Resource 中找到info.plist
2.Copy Pods Resources
在 target -> Build phase > Copy Pods Resources -> Output Files，移除${TARGET_BUILD_DIR}/${UNLOCALIZED_RESOURCES_FOLDER_PATH}

## Xcode突然没有智能提示
1.preferences->location->DerivedData 点击箭头定位到DerivedData 目录下，删除DerivedData文件夹
2.执行command + shift + k 和 command + option + shift + k，重启xcode


## 未完待续
