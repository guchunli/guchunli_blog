---
title: iOS使定时器NSTimer在程序进入后台仍然运行的方法
date: 2017-07-05 16:16:46
categories: 学习
tags: [iOS,NSTimer,后台]
---

## 如何使定时器NSTimer在程序进入后台仍然运行
1.Info.plist中，`Required background modes`添加`App plays audio or streams audio/video using AirPlay`
<!--more-->
2.`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions`中添加以下代码：
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

## 获取APP进入后台的时间

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

