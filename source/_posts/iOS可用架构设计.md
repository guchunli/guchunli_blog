---
title: iOS可用架构设计
date: 2017-07-21 10:43:44
categories: 笔记
tags: [架构]
---

## 项目目录结构
* AppDelegate：应用入口
    - AppDelegate：生命周期
    - AppDelegate+AppService：推送，IM，支付等
<!--more-->
* Base：基类
    - TabbarController
    - NavigationController
    - WebViewController
    - BaseViewController
    - NoDataViewController
* Modules：功能模块，APP的主要业务逻辑，MVC
    - Home
        + ViewController
        + View
        + Model
        + Logic
        + Service
* Manager：管理模块，用户登录退出，网络状态监听，广告页，应用介绍页等
    - AppManager
    - UserManager
* Utils：工具类，三方框架的二次封装，自己定义的工具类等
    - Network
    - Category
    - HUD
    - ADPage
* Frameworks：第三方框架，支付，IM等
* Define：全局宏定义，屏幕宽高、接口地址、字体颜色、key、secret
* Resource：资源文件，图片，音视频等

参考链接：[iOS 从0到1搭建高可用App框架](http://www.jianshu.com/p/d553096914ff)
