---
title: iOS获取定位授权
date: 2017-06-28 15:52:37
categories: 笔记
tags: [定位,授权]
toc:
---

手动调用CLLocationManager对象的requestAlwaysAuthorization方法，会弹出定位授权提示弹框。调用该方法需要在Info.plist中设置NSLocationAlwaysUsageDescription的值，这个值会显示在系统提示框中。 
* 仅执行 requestWhenInUseAuthorization，弹框中提示信息：允许"XXX"在您使用该应用时访问您的位置吗？，并且只有允许或不允许选择项。执行requestAlwaysAuthorization或同时requestWhenInUseAuthorization 弹框中提示信息：允许"XXX"访问您的位置？，并且这时会有三个选择项：仅在使用应用期间，始终允许，不允许。
* 注意：CLLocationManager的实例需要设置为全局变量，否则定位授权提示弹框会出现一闪而过的问题。 
```
if ([CLLocationManager locationServicesEnabled]) {
    // 创建位置管理者对象
    self.lcManager = [[CLLocationManager alloc] init];
    self.lcManager.delegate = self; // 设置代理
    // 设置定位距离过滤参数 (当本次定位和上次定位之间的距离大于或等于这个值时，调用代理方法)
    self.lcManager.distanceFilter = 100;
    self.lcManager.desiredAccuracy = kCLLocationAccuracyBest; // 设置定位精度(精度越高越耗电)
    [self.lcManager requestAlwaysAuthorization];
    [self.lcManager requestWhenInUseAuthorization];
    [self.lcManager startUpdatingLocation]; // 开始更新位置
}

```

* 代理方法监听状态变化：
```
-(void)locationManager:(CLLocationManager *)manager didChangeAuthorizationStatus:(CLAuthorizationStatus)status{

    switch (status) {
    case kCLAuthorizationStatusNotDetermined:
    {
        NSLog(@"用户还未决定授权");
        break;
    }
    case kCLAuthorizationStatusRestricted:
    {
        NSLog(@"访问受限");
        break;
    }
    case kCLAuthorizationStatusDenied:
    {
        // 类方法，判断是否开启定位服务
        if ([CLLocationManager locationServicesEnabled]) {
            NSLog(@"定位服务开启，被拒绝");
        } else {
            NSLog(@"定位服务关闭，不可用");
        }
        break;
    }
    case kCLAuthorizationStatusAuthorizedAlways:
    {
        NSLog(@"获得前后台授权");
        break;
    }
    case kCLAuthorizationStatusAuthorizedWhenInUse:
    {
        NSLog(@"获得前台授权");
        break;
    }
    default:
        break;
    }
}
```
