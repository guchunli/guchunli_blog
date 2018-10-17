---
title: to study
date: 2018-06-27 15:48:59
categories: 笔记
tags: 
toc:
---

工厂模式

UITableView滑动不加载，滑动停止加载
UICollectionViewLayout自定义

APP配置多环境变量和制作马甲包，私有APP，私有库，私有API
operation和GCD区别
工厂模式
AsyncDisplayKit
ComponentKit
MVVM
RESTFUL API
jenkins 阿里云 自动化测试



通知中心-字典  timer-retain atomic-lock，mutablearray

* componentkit，三大特性
声明式 Declarative:

Instead of implementing -sizeThatFits: and -layoutSubviews and positioning subviews manually, you declare the subcomponents of your component (here, we say “stack them vertically”). 
相比原生设置UI需要手动设置位置，声明式的特性只需要我们做一个声明描述即可，比如垂直排列元素。 
非常方便。

函数式Functional:

Data flows in one direction. 
Methods take data models and return totally immutable components. 
数据单向流动，数据流向UI 
根据Data获取对应的不可变的Components组件

When state changes, ComponentKit re-renders from the root and reconciles the two component trees from the top with as few changes to the view hierarchy as possible.

状态改变时，ComponentKit会重新绘制。这时候有oldState和newState，会仔细核对两个组件树(Component tree),从root node到top node 尽量使用最少的重绘来更新view层级结构。

组合式Composable:

Here FooterComponent is used in an article, but it could be reused for other UI with a similar footer. 
Reusing it is a one-liner. 
CKFlexboxComponent is inspired by the flexbox model of the web and can easily be used to implement many layouts. 
比如上文demo中的FooterComponent可以复用在别的组件里。 
CKFlexboxComponent是类似于flexbox的Component。


APNS,cookie
APNS本地消息存储

layoutsubview调用时机




[百思不得姐demo](https://github.com/targetcloud/baisibudejie)

