---
title: iOS常用第三方框架
date: 2017-07-21 10:26:37
categories: 学习
tags: [框架,待补充]
---

## 网络
1.AFNetworking

2.PPNetworkHelper
* 对AFNetworking 3.x 与YYCache的二次封装
* 简单易用，包含了缓存机制，控制台可以直接打印json中文字符
<!--more-->

3.YTKNetwork
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

## 键盘监听管理工具
* IQKayboardManager

## Badge
* WZLBadge

## 日历
* GFCalendar
* FSCalendar

## 直播
* LFLiveKit
* IJKMediaFramework

## 视频播放
* VIMVideoPlayer

## VR播放器
* MDVRLibrary

待补充


