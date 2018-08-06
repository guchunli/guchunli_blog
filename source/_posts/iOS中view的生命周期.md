---
title: iOS中view的生命周期
date: 2018-08-03 17:18:34
categories: 笔记
tags: [生命周期]
toc:
---

## app 的生命周期
### APP的5种状态
* Not running未运行：app没启动或被迫终止。
* Inactive未激活：当前应用正在前台运行，但是并不接收事件（当前或许正在执行其它代码）。一般每当应用要从一个状态切换到另一个不同的状态时，中途过渡会短暂停留在此状态。唯一在此状态停留时间比较长的情况是：当用户锁屏时，或者系统提示用户去响应某些（诸如电话来电、有未读短信等）事件的时候。
* Active激活：当前应用正在前台运行，并且接收事件。这是应用正在前台运行时所处的正常状态。
* Backgroud后台：程序在后台而且能执行代码，大多数程序进入这个状态后会在在这个状态上停留一会。时间到之后会进入挂起状态(Suspended)。经过特殊的请求后可以长期处于Backgroud状态。
* Suspended挂起：程序在后台不能执行代码。系统会自动把程序变成这个状态而且不会发出通知。当挂起时，程序还是停留在内存中的，当系统内存低时，系统就把挂起的程序清除掉，为前台程序提供更多的内存。

### 执行流程
application:didFinishLaunchingWithOptions:   这是程序启动时调用的函数。
applicationDidBecomeActive:  应用在准备进入前台运行时执行的函数。
applicationWillResignActive:   应用当前正要从前台运行状态离开时执行的函数。
applicationDidEnterBackground: 此时应用处在background状态，并且没有执行任何代码，未来将被挂起进入suspended状态。
applicationWillEnterForeground:  当前应用正从后台移入前台运行状态，但是当前还没有到Active状态时执行的函数。
applicationWillTerminate:  当前应用即将被终止，在终止前调用的函数。如果应用当前处在suspended，此方法不会被调用。

![app运行流程](/assets/20180803_lifecycle/AppDelegate.png)

* APP启动
application:didFinishLaunchingWithOptions:
applicationDidBecomeActive

* 按home键
applicationWillResignActive
applicationDidEnterBackground

* 再次回到前台
applicationWillEnterForeground
applicationDidBecomeActive

* 当URL请求到达时，如果你的应用没在正在运行，则会被启动并且移到前台运行以打开URL。
application:didFinishLaunchingWithOptions:
application:openURL:sourceApplication:
applicationDidBecomeActive

* 当URL请求到来时，如果你的应用正在background运行或被suspended，它将会被移到前台以打开URL。 
从第三方分享回来：
applicationWillEnterForeground
application:openURL:sourceApplication:
applicationDidBecomeActive

* 当一个基于警告的中断（诸如电话来电）发生时，应用会暂时从active状态切换到Inactive状态，以给系统提供机会提示用户，让用户决定如何处理。在用户决定如何处理此中断警告之前，应用将一直处于Inactive状态。 在用户做出选择后，当前应用或者回到active状态继续运行，或者直接切换到background状态以让位于其它的应用运行。
对于基于警告的中断将会导致用户暂时对应用失去控制。当前应用继续在前台foreground运行，但是不再接收任何触控事件。（事实上，应用只是不再接收触控类事件，其它类型的事件比如accelerometer事件，和通知Notification，应用仍然接收。）

* 当系统内存不足时，系统会强行关闭那些尚在内存中但处于后台状态的 app，以腾出足够的内存供使用。但是那些被强行关闭的程序不会调用任何UIApplicationDelegate的委托方法，只会得到一个KILL 信号。当我们长按app icon，强行关闭app，也是同样的处理过程。

## ViewController 的生命周期
```
init
//第一次加载到内存
loadView                //加载view
viewDidLoad             //view加载完毕

//显示布局相关
viewWillAppear          //view将要显示
viewWillLayoutSubViews  //view将要布局子视图
viewDidLayoutSubViews   //view完成布局子视图
viewDidAppear           //view完全显示
viewWillDisAppear       //view即将消失
viewDidDisappear        //view完全消失
```

* init
init里不要出现创建view的代码。良好的设计，在init里应该只有相关数据的初始化，而且这些数据都是比较关键的数据。init里不要掉self.view，否则会导致viewcontroller创建view。（因为view是lazyinit的）。
* loadView
loadView只初始化view，一般用于创建比较关键的view如tableViewController的tabView，UINavigationController的navgationBar，不可掉用view的getter（在掉super loadView前），最好也不要初始化一些非关键的view。如果你是从nib文件中创建的viewController在这里一定要首先调用super的loadView方法，但建议不要重载这个方法。
* didReceiveMemoryWarning
当app收到内存警告的时候会发消息给视图控制器。
app从来不会直接调用这个方法，而是当系统确定可用内存不足的时候采取调用。
如果你想覆写这个方法来释放一些控制器使用的额外内存，你应该在你的实现方法中调用父类的实现方法。
* viewWillUnload：（iOS6废除）
* viewDidUnload：（iOS6废除）

### view的加载过程
![view的加载过程](/assets/20180803_lifecycle/view_lifecycle.png)

1.View创建:loadView
1.从Storyboard／Xib加载视图时会调用的方法
1).initWithCoder
2).awakeFromNib: 此时frameh还没有完成。

2.手写代码：
1).initWithCoder
2).initWithFrame，创建时init会调用此方法。不过此时frame为0,除非显示调用此方法,frame才会有值。

view采用懒加载的方式，只有用到view时才会被创建，即才会被调用loadView->viewDidLoad这一系列函数，控制器的View是延迟加载的: 创建控制器并不一定会创建控制器的view,等用到时再加载。

### layoutSubViews
* layoutSubViews方法由系统来调用，不能程序员来调用。
* 可以调用setNeedsLayout方法进行标记,以保证在UI下个刷屏系统中会调用layoutSubviews。
* 可以调用layoutIfNeeded直接请求系统调用layoutSubviews。

layoutSubViews的被调用时机：
1.addSubView会触发layoutSubviews,比如ivewA add ViewB，第一次添加A和B的layoutSubviews都会被调用，而第二次( viewA 已经有了viewB)只调用viewB的。
2.view的frame改变会触发layoutSubViews。
3.滚动一个UIScrollView会触发layoutSubviews。
4.旋转Screen会触发UIView的layoutSubviews。
5.改变transform属性时，当然frame也会变。
6.处于key window的UIView才会被调用( 程序同一时间只有一个window为keyWindow，可以简单理解为显示在最前面的window的keywindow)。

## UIScrollView 的代理方法调用顺序
```
/***手离开屏幕时没有移动的速度（Pan）***/
//开始移动（并没有移动只是touch上去）
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView;  

//移动之后就会改变contentOffset，所以就会调用改方法
- (void)scrollViewDidScroll:(UIScrollView *)scrollView; 

//将要结束移动（手指停止移动但是没有离开屏幕）
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset  
//结束移动和是否要减速
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate;

/***手离开屏幕时有速度（Swipe）***/
//将要开始减速
- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView;
//减速时的屏幕出现content Offset改变
- (void)scrollViewDidScroll:(UIScrollView *)scrollView; 
//结束减速  
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView;   
```


参考文章：[IOS app生命周期](https://www.cnblogs.com/wodemeng/p/ios1.html)
[ios中的view生命周期](https://www.jianshu.com/p/42eb5a930d66)
