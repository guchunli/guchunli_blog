---
title: iOS使定时器NSTimer在程序进入后台仍然运行的方法
date: 2017-07-05 16:16:46
categories: 笔记
tags: [iOS,NSTimer,后台]
---

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

