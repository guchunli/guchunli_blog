---
title: iOS通过外部应用打开APP
date: 2018-06-12 16:11:28
categories: 笔记
tags: [打开,唤醒]
toc:
---

微信屏蔽了外部唤醒其他app，实现从微信浏览器唤醒APP的几种方法：
## 1.加入微信白名单
## 2.使用腾讯应用宝，魔窗第三方服务
## 3.微信右上角有个“更多”，点击后选择在浏览器中打开
<!--more-->
例如：`test://com.xxx?name=zhangsan&pwd=123`
* URL Schemes：xxx，url.scheme = `test`。
* URL Identifier：是自定义的 URL scheme 的名字，一般采用反转域名的方法保证该名字的唯一性，比如 com.domain。url.host =`com.xxx`。
* URL query: 参数，url.query = `name=zhangsan&pwd=123`。
### 测试
检验URL是否能够跳转：在浏览器中输入`URL Schemes://`，弹出提示框："是否打开XXX"，那么证明该URL是支持跳转的。

## 4.universal links
### APPID打开Associated Domains

### 配置你的App的Universal Links（通用链接）
 targets->Capabilities->Associated Domains
 添加`applinks:domain.com`，可以配置多个

### apple-app-site-association
* json文件，但文件不要加后缀名。上传apple-app-site-association到HTTPS(注意：配置的网站必须是https)服务器根目录下，可以通过Get请求拉取到：https://domain.com/apple-app-site-association。
apple-app-site-association文件内容如下：
```
{
    "applinks": {
        "apps": [],
        "details": [
            {
                "appID": "TeamID.com.domain.App",
                "paths":[ "*" ]
            }
        ]
    }
}
```

* appID：APPID的Prefix.bundleID
* paths：支持Universal Link，也就是可以跳转的路径。*代表此域名下所有路径都支持。

### AppDelegate
```
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray * _Nullable))restorationHandler{

    if (![userActivity.activityType isEqualToString:NSUserActivityTypeBrowsingWeb]) {
        return YES;
    }

    //读取url地址
    NSURL *webUrl = userActivity.webpageURL;
    NSLog(@"%@",webUrl);

    return YES;
}
```

### 测试
检验URL是否能够跳转：在系统原生App中（如短信、邮件等）长按URL，如果弹出的选项中有在“your app”中打开，那么证明该URL是支持跳转的。

### 注意事项：
1.配置的网站必须是https，不能为http；

2.[重要]展示地址和打开的地址不能在一个域名下，比如展示页的地址是https://a.domain.com/?id=10，打开的如果是https://a.domain.com/app/?id=10，系统默认是打开页面，而不是触发通用链接打开app；

3.[重要]配置玩证书之后需要更新证书，才能内部打包和提测上传成功；

4.通用链接可被屏蔽，点击右上角配置的链接之后，通用链接就失效了，解决方案详见：《iOS通用链接（Universal Links）突然点击无效的解决方案》。

## 打开APP设置页面
```
[[UIApplication sharedApplication]openURL:[NSURL URLWithString:UIApplicationOpenSettingsURLString] ];
```

### 打开APP设置具体页面：
```
NSURL *url = [NSURL URLWithString:@"App-Prefs:root=LOCATION_SERVICES"];
if ([[UIApplication sharedApplication] canOpenURL:url]) {

    if (@available(iOS 10, *) ) {

        //iOS10.0以上  使用的操作
        [[UIApplication sharedApplication] openURL:url options:@{} completionHandler:nil];

    } else {
        //iOS10.0以下  使用的操作
        [[UIApplication sharedApplication] openURL:url];
    }
}
```

* 在iOS10以上的系统，"App-Prefs"同样会跳到设置页，"prefs"或"Prefs"不会响应以上的跳转方法，且会报如下错误信息：
```
-canOpenURL: failed for URL: "prefs:root=LOCATION_SERVICES" - error: "The operation couldn’t be completed. (OSStatus error -10814.)"
```

附跳转到具体设置页面代码：
```
Swift

UIApplication.sharedApplication().openURL(NSURL(string:"prefs:root=General")!)
```

```
Objective-c

[[UIApplication sharedApplication] openURL:[NSURL URLWithString:@"prefs:root=General"]];

prefs:root=General&path=About
prefs:root=General&path=ACCESSIBILITY
prefs:root=AIRPLANE_MODE
prefs:root=General&path=AUTOLOCK
prefs:root=General&path=USAGE/CELLULAR_USAGE
prefs:root=Brightness    //打开Brightness(亮度)设置界面
prefs:root=Bluetooth    //打开蓝牙设置
prefs:root=General&path=DATE_AND_TIME    //日期与时间设置
prefs:root=FACETIME    //打开FaceTime设置
prefs:root=General    //打开通用设置
prefs:root=General&path=Keyboard    //打开键盘设置
prefs:root=CASTLE    //打开iClound设置
prefs:root=CASTLE&path=STORAGE_AND_BACKUP    //打开iCloud下的储存空间
prefs:root=General&path=INTERNATIONAL    //打开通用下的语言和地区设置
prefs:root=LOCATION_SERVICES    //打开隐私下的定位服务
prefs:root=ACCOUNT_SETTINGS
prefs:root=MUSIC    //打开设置下的音乐
prefs:root=MUSIC&path=EQ    //打开音乐下的均衡器
prefs:root=MUSIC&path=VolumeLimit  //打开音乐下的音量
prefs:root=General&path=Network    //打开通用下的网络
prefs:root=NIKE_PLUS_IPOD
prefs:root=NOTES    //打开设置下的备忘录设置
prefs:root=NOTIFICATIONS_ID    //打开设置下的通知设置
prefs:root=Phone    //打开电话设置
prefs:root=Photos    //打开设置下照片和相机设置
prefs:root=General&path=ManagedConfigurationList    //打开通用下的描述文件
prefs:root=General&path=Reset    //打开通用下的还原设置
prefs:root=Sounds&path=Ringtone
prefs:root=Safari    //打开设置下的safari设置
prefs:root=General&path=Assistant    //打开siri不成功
prefs:root=Sounds    //打开设置下的声音设置
prefs:root=General&path=SOFTWARE_UPDATE_LINK    //打开通用下的软件更新
prefs:root=STORE    //打开通用下的iTounes Store和App Store设置
prefs:root=TWITTER    //打开设置下的twitter设置
prefs:root=FACEBOOK    //打开设置下的Facebook设置
prefs:root=General&path=USAGE    //打开通用下的用量
prefs:root=VIDEO
prefs:root=General&path=Network/VPN        //打开通用下的vpn设置
prefs:root=Wallpaper    //打开设置下的墙纸设置
prefs:root=WIFI    //打开wifi设置
prefs:root=INTERNET_TETHERING
```



官方配置文件：[https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW2](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW2)

参考文档：[微信中通过页面(H5)直接打开本地app的解决方案](https://www.cnblogs.com/vipstone/p/7496008.html?utm_source=tuicool&utm_medium=referral)

[Universal Link 前端部署采坑记](http://www.cocoachina.com/ios/20170904/20463.html)
