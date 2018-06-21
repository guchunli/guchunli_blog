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
* URL Schemes：xxx
* URL Identifier：是自定义的 URL scheme 的名字，一般采用反转域名的方法保证该名字的唯一性，比如 com.domain
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



官方配置文件：[https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW2](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html#//apple_ref/doc/uid/TP40016308-CH12-SW2)

参考文档：[微信中通过页面(H5)直接打开本地app的解决方案](https://www.cnblogs.com/vipstone/p/7496008.html?utm_source=tuicool&utm_medium=referral)

[Universal Link 前端部署采坑记](http://www.cocoachina.com/ios/20170904/20463.html)
