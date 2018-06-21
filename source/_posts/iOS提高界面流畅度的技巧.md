---
title: iOS提高界面流畅度的技巧
date: 2017-12-19 19:10:30
categories: 笔记
tags: [流畅]
toc:
---

## CPU资源消耗原因和解决方案
### 对象创建
* 尽量用轻量的对象代替重量的对象
* 使用storyboard创建对象比通过代码消耗资源多
<!--more-->
* 尽量推迟对象创建的时间
* 能复用的对象尽量使用复用

### 对象调整
* 尽量避免调整视图层次，添加或移除视图

### 布局计算
* 视图布局的计算是APP中最常见消耗CPU的地方，尽量在`后台线程`计算好视图布局并对视图布局进行`缓存`，尽量`提前`算好布局，在需要时一次性调整好对应属性，而不要多次，频繁的计算、调整这些属性

### autolayout
* autolayout对于复杂视图来说常常会产生严重的性能问题，尽量使用category来快捷地获取视图的width,height等属性。

### 文本计算、渲染
* 可以参考UILabel 内部的实现方式：用 [NSAttributedString boundingRectWithSize:options:context:] 来计算文本宽高，用 -[NSAttributedString drawWithRect:options:context:] 来绘制文本，注意需要放到后台线程进行以避免阻塞主线程。
* 用 TextKit 或最底层的 CoreText 对文本异步绘制。CoreText 对象占用内存较少，可以缓存下来以备稍后多次渲染。屏幕上能看到的所有文本内容控件，在底层都是通过 CoreText 排版、绘制为 Bitmap 显示的。

## GPU资源消耗原因和解决方案
* GPU的作用：接收提交的纹理和顶点描绘，应用变换，混合和渲染，并且输出到屏幕上。

### 纹理的渲染
* 尽量减少在短时间内大量图片的显示，尽可能多张图片合成为一张进行显示。

### 视图的混合
* 尽量减少视图数量和层次

### 图形的生成
* 预渲染：避免使用CALayer的边框，圆角，阴影，遮罩等属性，为了避免离屏渲染，尽量在后台线程预先绘制好对应内容。

## AsyncDisplayKit(ASDK)

## FDTemplateLayoutCell
* 使用 TableView 的预估高度的功能，并把每个 Cell 高度缓存下来。

## 常用技巧
### 预渲染
* 在后台线程将头像预先渲染为圆形并单独保存到一个 ImageCache 中去。

### 异步绘制
* 当 TableView 快速滑动时，会有大量异步绘制任务提交到后台线程去执行。可参考`YYAsyncLayer`

### 全局并发控制
* YYDispatchQueuePool

### 更高效的异步图片加载

## product->profile


参考文章：[iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)
