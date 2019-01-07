---
title: OC中的设计模式
copyright: true
date: 2018-12-14 11:21:02
categories: 笔记
tags: [面试]
toc:
---

### block
[OC中的block](https://guchunli.github.io/2017/07/01/OC中的block/)
实质是OC对象
<!--more-->
* 为什么在默认情况下无法修改被block捕获的变量？
Block只捕获Block中会用到的变量。由于只捕获了自动变量(自动变量是以值传递方式传递到Block的构造函数里面)的值，并非内存地址，所以Block内部不能改变自动变量的值。Block捕获的外部变量可以改变值的是静态变量，静态全局变量，全局变量。

#### 函数指针

### 为什么代理用weak，delegate和dataSource有什么区别，delegate和block的区别
1.weak指明该对象不会保持delegate，delegate这个对象的销毁由外部控制，strong会强引用delegate，外界不能销毁delegate，会导致循环引用。
2.datasource是关于数据的，都有哪些属性，值等；delegate时关于操作的，有什么方法可以调用。
3.delegate和block都可以回调。delegate是个对象，调用代理协议函数完成操作。block是传递一个函数指针，利用函数指针执行来进行回调。内存管理上，delegate不需要保存引用，block对数据又copy的处理。

### KVO

### delegate

### 观察者模式

#### delegate与通知的区别
* 协议有控制链(has-a)的关系，通知没有。
* 通知：一对多。代理/block：一对一。
* OC中的多继承用委托代理实现

### 单例

### MVC/MVP/MVVM
1.MVC
视图（view）：用户界面
模型（model）：数据保存
控制器（controller）：业务逻辑

通信方式：view->controller->model->view

2.MVP
将controller改名为presenter
各部分之间的通信都是双向的，view与model不发生联系，都通过presenter传递，view薄，controller厚。

通信方式：view<- &->presenter<- &->model

3.MVVM
将presenter改名为viewmodel
双向绑定，view的变动自动反应在viewmodel，反之亦然。

通信方式：view<->viewmodel<- &->model
