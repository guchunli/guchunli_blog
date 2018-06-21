---
title: iOS计时器timer学习笔记
date: 2018-06-20 18:12:53
categories: 学习
tags: [计时器,timer]
toc: true
---

## 创建
```
initWithFireDate
timerWithTimeInterval
scheduledTimerWithTimeInterval
```
<!--more-->
* 如果使用timerWithTimeInterval或initWithFireDate构造，需要手动添加到runloop上
* 使用`scheduledTimerWithTimeInterval`则不需要，scheduledTimerWithTimeInterval除了`构造timer`，还会把timer添加到`当前线程的runloop`
```
- (void)timer1 {
    self.timer = [[NSTimer alloc] initWithFireDate:[NSDate dateWithTimeIntervalSinceNow:3] interval:3 target:self selector:@selector(timerTest:) userInfo:nil repeats:YES];
    // 需要添加到runloop才能触发
    [[NSRunLoop currentRunLoop] addTimer:self.timer forMode:NSDefaultRunLoopMode];
}

- (void)timer2 {
    self.timer = [NSTimer timerWithTimeInterval:2 target:self selector:@selector(timerTest:) userInfo:nil repeats:YES];
    // 正常触发
    [[NSRunLoop currentRunLoop] addTimer:self.timer forMode:NSDefaultRunLoopMode];
}

- (void)timer3 {
    // 自动添加到runloop
    self.timer = [NSTimer scheduledTimerWithTimeInterval:2 target:self selector:@selector(timerTest:) userInfo:nil repeats:YES];
}
- (void)timerTest:(NSObject *)obj {
    NSLog(@"time fire");
}
```

## 触发
* NSTimer只有被添加到runloop才能生效，NSTimer在添加到runloop时，timer开始计时，即使runloop没有开启（run），在构造NSTimer的时候，如果不是马上开始计时，可以先使用timerWithTimeInterval再手动加入runloop上
* 没有添加到runloop的timer，调用fire的时候会立即触发，并且只触发一次（如果repeat:YES），该方法触发不影响计时器原本的计时，只是新增一次触发
* 当NSTimer进入后台的时，NSTimer计时暂停，进入前台继续

## NSTimer与runloop
```
[[NSRunLoop currentRunLoop] addTimer:self.timer forMode:NSRunLoopCommonModes];
```

## 循环引用
NSTimer在构造函数会对target强引用，在调用invalidate时，会移除去target的强引用
NSTimer被加到Runloop的时候，会被runloop强引用持有，在调用invalidate的时候，会从runloop删除
当定时器是不重复的（repeat=NO），在执行完触发函数后，会自动调用invalidate解除runloop的注册和接触对target的强引用，如果repeats参数为YES，则需要程序员手动调取invalidate方法才能释放timer对target和userIfo的强引用。
由于NSTimer被加到runloop的时候会被runloop强引用，故如果使用scheduledTimerWithTimeInterval构造函数时，我们可以在viewcontroller使用weak引用NSTimer，而`当调用invalidate时，self.timer会被自动置为nil`
```
@property (nonatomic, weak) NSTimer *timer;

- (void)viewDidLoad {
    [super viewDidLoad];
    
    self.timer = [NSTimer scheduledTimerWithTimeInterval:2 target:self selector:@selector(timerTest:) userInfo:nil repeats:YES];
    }

```
invalidate方法通常不能放在NStimer.target.dealloc里面，因为NSTimer会对target强引用，而如果target对NSTimer强引用就会造成循环引用
所以通常我们不能在dealloc方法让[timer invalidate], 因为timer在invalidate之前，会引用self（通常是ViewController），导致self无法释放，可以在viewDidDisappear或显式调用timer的invalidate方法

invalidate是唯一让timer从runloop删除的方法，也是唯一去除对target强引用的方法

## NSTimer与多线程
如果我们不在主线程使用Timer的时候，即使我们把timer添加到runloop，也不能被触发，因为主线程的runloop默认是开启的，而其他线程的runloop默认没有实现runloop，并且在后台线程使用NSTimer不能通过fire启动定时器，只能通过runloop不断的运行下去
非主线程需要手动运行runloop，run方法会阻塞，直到没有输入源的时候返回（例如：timer从runloop中移除，invalidate）
```
- (void)viewDidLoad {
    [super viewDidLoad];

    // 使用新线程
    [NSThread detachNewThreadSelector:@selector(startNewThread) toTarget:self withObject:nil];
}

- (void)startNewThread {
    self.timer = [NSTimer timerWithTimeInterval:2 target:self selector:@selector(timerTest:) userInfo:nil repeats:YES];

    // 添加到runloop
    NSRunLoop *runLoop = [NSRunLoop currentRunLoop];
    [runLoop addTimer:self.timer forMode:NSDefaultRunLoopMode];

    //手动运行runloop
    [runLoop run]
}
```

### GCD的定时器
如果对精度有要求，可以使用GCD的定时器
<!--NSTimer不支持暂停和继续，如果需要可以使用GCD的定时器-->

### NSTimer的暂停/重启
```
[timer setFireDate:[NSDate distantFuture]];//停止
[timer setFireDate:[NSDate distantPast]];//运行
```


## 后台运行
### 让App支持后台运行（运行音频）（在后台可以触发）
Info.plist中，`Required background modes`添加`App plays audio or streams audio/video using AirPlay`
`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions`中添加以下代码：
```
NSError *setCategoryErr = nil;
NSError *activationErr  = nil;
[[AVAudioSession sharedInstance]
setCategory: AVAudioSessionCategoryPlayback
error: &setCategoryErr];
[[AVAudioSession sharedInstance]
setActive: YES
error: &activationErr];
self.window.backgroundColor = [UIColor whiteColor];
[self.window makeKeyAndVisible];
```

3.程序进入后台进行以下操作
```
- (void)applicationDidEnterBackground:(UIApplication *)application {

UIApplication* app = [UIApplication sharedApplication];
__block UIBackgroundTaskIdentifier bgTask;

/*注册一个后台任务，告诉系统我们需要向系统借一些事件*/
bgTask = [app beginBackgroundTaskWithExpirationHandler:^{
dispatch_async(dispatch_get_main_queue(), ^{
if (bgTask != UIBackgroundTaskInvalid)
{
/*销毁后台任务标识符*/
/*不管有没有完成，结束background_task任务*/
bgTask = UIBackgroundTaskInvalid;
}
});
}];

dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
dispatch_async(dispatch_get_main_queue(), ^{
if (bgTask != UIBackgroundTaskInvalid)
{
/*销毁后台任务标识符*/
/*不管有没有完成，结束background_task任务*/
bgTask = UIBackgroundTaskInvalid;
}
});
});
}
```

### 记录离开和进入App的时间，手动控制计时器（在后台不能触发）
1.AppDelegate 发送通知
```
// APP进入后台
- (void)applicationDidEnterBackground:(UIApplication *)application
{
NSLog(@"APP进入后台");
[[NSNotificationCenter defaultCenter]postNotificationName:UIApplicationDidEnterBackgroundNotification object:nil];
}

// APP将要从后台返回
- (void)applicationWillEnterForeground:(UIApplication *)application
{
NSLog(@"APP进入前台");
[[NSNotificationCenter defaultCenter]postNotificationName:UIApplicationWillEnterForegroundNotification object:nil];
}
```

2.创建一个管理类：EnterBackgroundManager，添加观察者，接受通知
```
@property(nonatomic,strong) id observer_enterBack;
@property(nonatomic,strong) id observer_enterFore;

//添加两个观察者
- (void)addObserverUsingBlock:(TGHandlerEnterBackgroundBlock)block {
__block CFAbsoluteTime enterBackgroundTime;

self.observer_enterBack = [[NSNotificationCenter defaultCenter]addObserverForName:UIApplicationDidEnterBackgroundNotification object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
if (![note.object isKindOfClass:[UIApplication class]]) {
enterBackgroundTime = CFAbsoluteTimeGetCurrent();
}
}];


__block CFAbsoluteTime enterForegroundTime;
self.observer_enterFore = [[NSNotificationCenter defaultCenter]addObserverForName:UIApplicationWillEnterForegroundNotification object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
if (![note.object isKindOfClass:[UIApplication class]]) {
enterForegroundTime = CFAbsoluteTimeGetCurrent();
CFAbsoluteTime timeInterval = enterForegroundTime-enterBackgroundTime;
NSLog(@"APP在后台持续 time = %lf s",timeInterval);
block? block(note, timeInterval): nil;
}
}];
NSLog(@"通知中心添加监听者：进入前后台")

}
//移除通知
-(void)removeNotificationObserver{

[[NSNotificationCenter defaultCenter] removeObserver:self.observer_enterBack];
[[NSNotificationCenter defaultCenter] removeObserver:self.observer_enterFore];
self.observer_enterBack = nil;
self.observer_enterFore = nil;
NSLog(@"通知中心释放监听者：进入前后台")
}
```

3.获取停留后台时间：
```
- (void)viewDidLoad {
[super viewDidLoad];
//监听APP在后台的时间
WS(weakSelf);
self.backgroundManager = [[EnterBackgroundManager alloc]init];
[self.backgroundManager addObserverUsingBlock:^(NSNotification * _Nonnull note, NSTimeInterval stayBackgroundTime) {
weakSelf.timeStr = weakSelf.timeStr - stayBackgroundTime;
}];
}

```

## performSelector
NSObject对象有一个performSelector可以用于延迟执行一个方法，其实该方法内部是启用一个Timer并添加到当前线程的runloop，原理与NSTimer一样，所以在非主线程使用的时候，需要保证线程的runloop是运行的，否则不会得到执行

## 总结
* NSTimer只有被注册到runloop才能起作用，fire不是开启定时器的方法，只是触发一次定时器的方法
* NSTimer会强引用target
* invalidate取消runloop的注册和target的强引用，如果是非重复的定时器，则在触发时会自动调用invalidate
通常我们自己封装GCD定时器使用起来更为方便，不会有这些问题


参考文章：[NSTimer学习笔记](https://segmentfault.com/a/1190000009404275)
