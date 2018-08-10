---
title: iOS中layoutSubviews
date: 2018-08-09 11:07:06
categories: 笔记
tags: [layoutSubviews,layout,display]
toc:
---

## 触发layoutSubviews方法的条件
* 初始化不会触发layoutSubviews，但是如果设置了不为CGRectZero的frame的时候就会触发。
* addSubview会触发layoutSubviews
* 设置view的Frame会触发layoutSubviews，当然前提是frame的值设置前后发生了变化
* 滚动一个UIScrollView会触发layoutSubviews
* 旋转Screen会触发父UIView上的layoutSubviews事件
* 改变一个UIView大小的时候也会触发父UIView上的layoutSubviews事件

## setNeedsLayOut / setNeedsDisplay
* 当需要刷新布局时，用setNeedsLayOut方法
* 当需要重新绘画时，调用setNeedsDisplay方法。

## setNeedsLayout / layoutIfNeeded
* layoutIfNeeded不一定会调用layoutSubviews方法。
* setNeedsLayout一定会调用layoutSubviews方法（有延迟，在下一轮runloop结束前）。
如果想在当前runloop中立即刷新，调用顺序应该是
```
[self setNeedsLayout];
[self layoutIfNeeded];
```
