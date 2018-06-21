---
title: iOS显示图片原色
date: 2018-01-31 16:08:07
categories: 笔记
tags: [图片,原色]
toc:
---

* 在设置导航栏的右图标时，可能会被系统设置为默认蓝等其他颜色，而不是图片本身的颜色。想要显示原来图片颜色使用以下：
<!--more-->
```
UIImage *image = [[UIImage imageNamed:@”arrow”]imageWithRenderingMode:UIImageRenderingModeAlwaysOriginal];
```
