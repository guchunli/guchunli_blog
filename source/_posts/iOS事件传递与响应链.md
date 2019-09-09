---
title: iOS事件传递与响应链
copyright: true
date: 2019-09-07 16:01:36
categories: 笔记
tags:
toc:
---


## UIResponder
* 继承UIResponder的的类，才能处理事件。
* UIApplication，UIView，UIViewController都是继承自UIResponder类
<!--more-->
## 事件分发与传递
分发与传递过程：UIApplication->UIWindow->UIView->subview
触摸事件的传递是由父控件传递到子控件的，如果父控件不能接受触摸事件，那么子控件就不可能接受触摸事件
三种情况不接受事件处理：
（1）alpha = 0.0~0.01
（2）hidden = YES
（3）userInteractionEnabled = NO

如何找到最合适的view来处理和接收事件?
从父控件到子控件寻找事件处理最合适的view的过程：
（1）判断当前控件能否接收事件
（2）用户手指的触摸点是否在它自己的身上
（3）从后往前遍历子控件（意思是先遍历后加入的子控件）
（4）如果没有找到符合条件的子控件，那么它自己就是最适合处理事件的控件

```
// 此方法返回的View是本次点击事件需要的最佳View
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event

// 判断一个点是否落在当前控件范围内
- (BOOL)pointInside:(CGPoint)point withEvent:(UIEvent *)event
```

```
// 因为所有的视图类都是继承BaseView
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    // 1.判断当前控件能否接收事件
    if (self.userInteractionEnabled == NO || self.hidden == YES || self.alpha <= 0.01) return nil;
    // 2. 判断点在不在当前控件
    if ([self pointInside:point withEvent:event] == NO) return nil;
    // 3.从后往前遍历自己的子控件
    NSInteger count = self.subviews.count;
    for (NSInteger i = count - 1; i >= 0; i--) {
        UIView *childView = self.subviews[I];
        // 把当前控件上的坐标系转换成子控件上的坐标系
        CGPoint childP = [self convertPoint:point toView:childView];
        UIView *fitView = [childView hitTest:childP withEvent:event];
        if (fitView) { // 寻找到最合适的view
            return fitView;
        }
    }
    // 4.如果没有找到符合条件的子控件，那么它自己就是最适合处理事件的控件
    return self;
}
```

## 响应者链
* 事件传递：事件由上往下传递（父控件传递给子控件），找到最合适的控件来处理这个事件之后，就会调用控件的touchBegin、touchMove、touchEnd三个方法
* 事件响应：传递给上一个响应者
如果view是控制器的view，则控制器就是上一个响应者
如果view不是控制器的view，则父控件就是上一个响应者
在视图层次结构的最顶级视图，如果也不能处理收到的事件或消息，则其将事件或消息传递给window对象进行处理
如果window对象也不处理，则其将事件或消息传递给UIApplication对象
如果UIApplication也不能处理该事件或消息，则将其丢弃

使用响应者链条找到当前view所属的控制器
```
- (UIViewController *)parentController
{
    UIResponder *responder = [self nextResponder];
    while (responder) {
        if ([responder isKindOfClass:[UIViewController class]]) {
            return (UIViewController *)responder;
        }
        responder = [responder nextResponder];
    }
    return nil;
}
```
