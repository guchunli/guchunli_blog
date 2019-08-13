---
title: iOS定时器相关
copyright: true
date: 2019-08-11 16:02:45
categories: 笔记
tags: 
toc: true
---

# NSTimer
## NSTimer 创建定时器
<!--more-->
1.timer分为invocation和selector两种调用方式，其实这两种区别不大，一般我们用selector方式较为方便。
```
NSMethodSignature  *signature = [[self class] instanceMethodSignatureForSelector:@selector(Timered:)];
NSInvocation* invocation = [NSInvocation invocationWithMethodSignature:signature];
invocation.target = self;
invocation.selector = @selector(Timered:);
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1 invocation:invocation repeats:YES];

NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];
```
2.scheduledTimerWith/timerWith/initWithFireDate
NSTimer是加到runloop中执行的，scheduledTimerWith创建并安排到runloop的default mode中。
* 添加到runloop，必须保证runloop存在
```
NSTimer *timer = [NSTimer timerWithTimeInterval:1.0 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];
[[NSRunLoop mainRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];
```

## 移除timer
invalidate作用：
* 1.将timer从runloop中移除
* 2.timer本身也会释放它持有资源，比如target、userinfo、block

timer移除时机：viewController的dealloc不可以，互相持有，不会调用
```
- (void)ViewWillDisappear{
    if (_timer)
        [_timer invalidate]; // 真正销毁NSTimer对象的地方
        _timer = nil; // 对象置nil是一种规范和习惯
    }
}
```


## fire
系统提供了一个- (void)fire;方法，调用它可以触发一次：
* 对于重复定时器，它不会影响正常的定时触发
* 对于非重复定时器，触发后就调用了invalidate方法，既使正常的还没有触发


## NSTimer 坑
### 坑一：子线程启动定时器问题
主线程默认启动了runloop，可是子线程没有默认的runloop，我们在子线程启动定时器是不生效的,解决:在子线程启动一下runloop就可以了。
```
dispatch_async(dispatch_get_global_queue(0, 0), ^{
//        NSTimer* timer = [NSTimer timerWithTimeInterval:1 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];
//        [[NSRunLoop currentRunLoop] addTimer:timer forMode:NSRunLoopCommonModes];
// scheduledTimerWith:创建并安排到runloop的default mode中
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];

// 子线程启动runloop
[[NSRunLoop currentRunLoop] run];
});
```

### 坑二：runloop的mode问题
UITrackingRunLoopMode和NSDefaultRunLoopMode都被标记为了common模式，所以只需要将timer的模式设置为NSRunLoopCommonModes，就可以在默认模式和追踪模式都能够运行。

### 坑三：循环引用问题
原因：就是NSTimer的target被强引用了，而通常target就是所在的控制器，他又强引用的timer，造成了循环引用
以下两种timer不会有循环引用：
（1）非repeat类型的。非repeat类型的timer不会强引用target，因此不会出现循环引用。
（2）block类型的，新api。iOS 10之后才支持，因此对于还要支持老版本的app来说，这个API暂时无法使用。当然，block内部的循环引用也要避免。
```
[NSTimer timerWithTimeInterval:1 repeats:YES block:^(NSTimer * _Nonnull timer) {
}];
[NSTimer scheduledTimerWithTimeInterval:1 repeats:YES block:^(NSTimer * _Nonnull timer) {
}];
```

* 强调：不是解决了循环引用，target就可以释放了，别忘了在持有timer的类dealloc的时候执行invalidate。
NSTimer* timer = [NSTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];
如上面代码，这个timer并没有被self引用，那么为什么self不会被释放呢？因为timer被加到了runloop中，timer又强引用了self，所以timer一直存在的话，self也不会释放。
`runloop会对timer有强引用，timer会对目标对象进行强引用`

解决循环引用：打破timer对target的强引用。

解决方式一：NSTimer会保留其目标对象
解释：将强引用的target变成了NSTimer的类对象。类对象本身是单例的，是不会释放的，所以强引用也无所谓。执行的block通过userInfo传递给定时器的响应函数timered:。循环引用被打破的结果是：
timer的使用者强引用timer。
timer强引用NSTimer的类对象。
timer的使用者在block中通过weak的形式使用，因此是被timer弱引用。
```
__weak id weakSelf = self;
NSTimer* timer = [NSTimer scheduledBlockTimerWithTimeInterval:1 repeats:YES blockTimer:^(NSTimer *timer) {
NSLog(@"block %@",weakSelf);
}];
```

```
@interface NSTimer (BlockTimer)

+ (NSTimer*)scheduledBlockTimerWithTimeInterval:(NSTimeInterval)interval repeats:(BOOL)repeats blockTimer:(void (^)(NSTimer *))block;

@end

@implementation NSTimer (BlockTimer)

+ (NSTimer*)scheduledBlockTimerWithTimeInterval:(NSTimeInterval)interval repeats:(BOOL)repeats blockTimer:(void (^)(NSTimer *))block{
// 将强引用的target变成了NSTimer的类对象。类对象本身是单例的，是不会释放的，所以强引用也无所谓。执行的block通过userInfo传递给定时器的响应函数timered:
NSTimer* timer = [NSTimer scheduledTimerWithTimeInterval:interval target:self selector:@selector(timered:) userInfo:[block copy] repeats:repeats];
return timer;
}

+ (void)timered:(NSTimer*)timer {
void (^block)(NSTimer *timer)  = timer.userInfo;
block(timer);
}

@end
```

解决方式二：NSProxy的方式
建立一个proxy类，让timer强引用这个实例，这个类中对timer的使用者target采用弱引用的方式，再把需要执行的方法都转发给timer的使用者。
```
//NSTimer *timer = [ProxyTimer scheduledProxyTimerWithTimeInterval:1 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];
NSTimer *timer = [ProxyTimer scheduledTimerWithTimeInterval:1 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];
```

```
@interface ProxyObject : NSProxy
@property (weak, nonatomic) id target;
+ (instancetype)proxyWithTarget:(id)target;
@end

@implementation ProxyObject

+ (instancetype)proxyWithTarget:(id)target {
ProxyObject* proxy = [[self class] alloc];
proxy.target = target;
return proxy;
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)sel{
return [self.target methodSignatureForSelector:sel];
}

- (void)forwardInvocation:(NSInvocation *)invocation{
SEL sel = [invocation selector];
if ([self.target respondsToSelector:sel]) {
[invocation invokeWithTarget:self.target];
}
}

@end

@implementation ProxyTimer
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti target:(id)aTarget selector:(SEL)aSelector userInfo:(nullable id)userInfo repeats:(BOOL)yesOrNo{
NSTimer* timer = [NSTimer scheduledTimerWithTimeInterval:ti target:[ProxyObject proxyWithTarget: aTarget] selector:aSelector userInfo:userInfo repeats:yesOrNo];
return timer;
}
@end
```

解决方式三：封装timer，弱引用target
类似NSProxy的方式，建立一个桥接timer的实例，弱引用target，让timer强引用这个实例
```
NSTimer *timer = [NSTimer scheduledWeakTimerWithTimeInterval:1 target:self selector:@selector(Timered:) userInfo:nil repeats:YES];
```

```
.h
@interface WeakTimer : NSObject

@property (nonatomic, weak) id target;
@property (nonatomic) SEL selector;

@end

@interface NSTimer(NormalTimer)
+ (NSTimer *)scheduledWeakTimerWithTimeInterval:(NSTimeInterval)ti target:(id)aTarget selector:(SEL)aSelector userInfo:(nullable id)userInfo repeats:(BOOL)yesOrNo;
@end

.m
@implementation WeakTimer
- (void)dealloc{
NSLog(@"timer dealloc");
}

- (void)timered:(NSTimer*)timer{
[self.target performSelector:self.selector withObject:timer];
}
@end

@implementation NSTimer(NormalTimer)
+ (NSTimer *)scheduledWeakTimerWithTimeInterval:(NSTimeInterval)ti target:(id)aTarget selector:(SEL)aSelector userInfo:(nullable id)userInfo repeats:(BOOL)yesOrNo{
WeakTimer *weakTimer = [[WeakTimer alloc] init];
weakTimer.target = aTarget;
weakTimer.selector = aSelector;
NSTimer* timer = [NSTimer scheduledTimerWithTimeInterval:ti target:weakTimer selector:@selector(timered:) userInfo:userInfo repeats:yesOrNo];
return timer;
}

@end
```

# GCD定时器
GCD定时器的好处是，他并不是加入runloop执行的，因此子线程也可以使用。也不会引起循环引用的问题。
注意:需要把timer声明为属性，否则，由于这种timer并不是添加到runloop中的，直接就被释放了。
```
{
dispatch_source_t timer;
}

__weak id weakSelf = self;
timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, dispatch_get_main_queue());
dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, 5 * NSEC_PER_SEC, 1 * NSEC_PER_SEC);
dispatch_source_set_event_handler(timer, ^{
[weakSelf timered];
});
dispatch_resume(timer);
// 取消定时器
//dispatch_suspend(timer);
```

# dispatch_after
dispatch_after内部也是使用的Dispatch Source。因此也避免了NSTimer的很多坑。
```
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{

});
```

# performSelector：after
这种方式通常是用于在延时后去处理一些操作，其内部也是基于将timer加到runloop中实现的。因此也存在NSTimer的关于子线程runloop的问题。这种调用方式的好处是可以取消。
```
[self performSelector:@selector(Timered:) withObject:nil afterDelay:3];
[NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(Timered:) object:nil];
```

# 延时操作
* 延时一次操作的选择
几种方式都是定时器，都可以实现延时操作。综合相比：如果只是单独一次的延时操作，NSTimer和GCD的定时器都显得有些笨重。performSelector方式比较合适，但是又收到了子线程runloop的限制。因此，dispatch_after是最优的选择。

## 取消延时操作：
NSTimer可以通过invalidate来停止定时器。
GCD的定时器可以调用dispatch_suspend来挂起。
performSelector：after可以通过cancelPreviousPerformRequestsWithTarget取消。
dispatch_after可以通过dispatch_block_cancel来取消。
```
//        dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
//            NSLog(@"把我取消");
//        });

@property (nonatomic,copy) void (^delayStartBlock)(void);

self.delayStartBlock = dispatch_block_create(0, ^{
NSLog(@"把我取消");
});
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5 * NSEC_PER_SEC)), dispatch_get_main_queue(),self.delayStartBlock);
dispatch_block_cancel(self.delayStartBlock);
```

[demo](https://github.com/guchunli/TimerDemo.git)

参考文章：[IOS定时器操作和NSTimer的各种坑](https://www.jianshu.com/p/ca579c502894)
