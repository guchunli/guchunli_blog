---
title: iOS常用第三方框架
date: 2017-07-21 10:26:37
categories: 笔记
tags: [第三方]
---

## 网络
1.PPNetworkHelper
* 对AFNetworking 3.x 与YYCache的二次封装
* 简单易用，包含了缓存机制，控制台可以直接打印json中文字符
<!--more-->

2..YTKNetwork
* 猿题库研发团队基于AFNetworking封装的iOS网络库
* 适合稍微复杂一些的项目，不适合个人的小项目

## 基础组件库
### YYKit
* YYModel— 高性能的 iOS JSON 模型框架。
* YYCache— 高性能的 iOS 缓存框架。
* YYImage— 功能强大的 iOS 图像框架。
* YYWebImage— 高性能的 iOS 异步图像加载框架。
* YYText— 功能强大的 iOS 富文本框架。
* YYKeyboardManager— iOS 键盘监听管理工具。
* YYDispatchQueuePool— iOS 全局并发队列管理工具。
* YYAsyncLayer— iOS 异步绘制与显示的工具。
* YYCategories— 功能丰富的 Category 类型工具库。
> 图像异步加载使用的NSURLConnection，还是建议使用SDWebImage。

## 等待加载提示
* MBProgressHUD
* SVProgressHUD
* UAProgressView

## 空白页面占位图
* [LYEmptyView](https://github.com/yangli-dev/LYEmptyView)

## 状态栏顶部通知
* [JDStatusBarNotification](https://github.com/jaydee3/JDStatusBarNotification)

## 键盘监听管理工具
* IQKayboardManager

## 相册选择图片
* KSPhotoBrowser

## Badge
* WZLBadge

## 日历
* GFCalendar
* FSCalendar

## 状态栏上方显示手机状态
* [GDPerformanceView-Swift](https://github.com/dani-gavrilov/GDPerformanceView-Swift)

## 跑马灯广告无线轮播
* [TXScrollLabelView](https://github.com/tingxins/TXScrollLabelView)
* CCPScrollView

## FAQ视图
* [FAQView](https://github.com/mukeshthawani/FAQView)

## 卡片界面
* [DMSwipeCards](https://github.com/D-32/DMSwipeCards)

## 模仿Airbnb首页过渡动画
* [JPAnimation](https://github.com/newyjp/JPAnimation)

## 界面转场动画集
* [WXSTransition](https://github.com/alanwangmodify/WXSTransition)

## iOS的CSS
* [StyleKit](https://github.com/146BC/StyleKit)

## 动画加号按钮
* [YGPulseView](https://github.com/YGeorge/YGPulseView)

## Swift 3快速异步助手
* [Do.this](https://github.com/BarakRL/Do.this)

## 密码输入切换框
* [UXPasscodeField](https://github.com/eddy-lau/UXPasscodeField)

## 美颜相机（仿faceU）
* [MARFaceBeauty](https://github.com/Maru-zhang/MARFaceBeauty)
* [XIUSDK--美图，美颜]()

## 多功能数据pickerView
* [DLPickerView](https://github.com/coder-zwz/DLPickerView)

## 直播
* LFLiveKit
* IJKMediaFramework

## 视频播放
* VIMVideoPlayer
* CLPlayer（tableViewCell、自动播放、全屏播放）

## VR播放器
* MDVRLibrary

## 知识整合，学习资源
* AxcUIKit
* [BigShow1949](https://github.com/BigShow1949/BigShow1949)

待补充


