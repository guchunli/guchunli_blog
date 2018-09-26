---
title: iPhoneX适配笔记
date: 2017-12-05 14:10:42
categories: 笔记
tags: [适配]
toc:
---

## 1.设置iPhoneX启动图片
<!--more-->
* 屏幕尺寸：5.8 英寸
* 屏幕密度：458 ppi
* 开发尺寸：375*812 pt
* 像素尺寸：1125*2436 px
* 倍图：@3x

## 2.safeArea
* 顶部有刘海： 状态栏44+导航栏44
* 底部有手势区域：bottom 34
* 四周有圆角

### 控制器生命周期
* viewDidLoad
* viewWillAppear
* viewSafeAreaInsetsDidChange（NEW）
* viewWillLayoutSubviews
* viewDidAppear

可以在viewSafeAreaInsetsDidChange后，设置safe area：
```
if #available(iOS 11.0, *) {
self.additionalSafeAreaInsets = UIEdgeInsetsMake(10, 0, 0, 34)
}
```

### 宏定义高度
* 适配iPhone x 导航栏高度
```
#define STATUSBAR_HEIGHT [[UIApplication sharedApplication] statusBarFrame].size.height
#define NAV_HEIGHT (STATUSBAR_HEIGHT + 44)
```

* 适配iPhone x 底栏高度
```
#define TabbarHeight     ([[UIApplication sharedApplication] statusBarFrame].size.height>20?83:49)
```

## 待补充


