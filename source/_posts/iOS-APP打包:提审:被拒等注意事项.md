---
title: iOS-APP打包/提审/被拒等注意事项
date: 2017-04-13 12:20:22
categories: 笔记
tags: [iOS,app store]
---

## 打包前注意点

1.改为正式环境
* 接口改成线上服务器
* 极光推送环境改为生产环境等

<!--more-->
2.版本号
target -> general：`Version`和`Build`版本号

3.签名
target -> general -> signing

4.删除断点，log

5.本地化，语言包

6.Capabilities -> background modes 
如果后台使用了位置，提交信息中应用描述添加免责声明
> 温馨提示：GPS在后台持续运行，会大大降低电池的寿命。

7.设备选择`Generic iOS Device`，注意不要选择真机或模拟器。

8.edit scheme: run,archive -> `debug` 改为 `release`

9.打包ad-hoc，进行内测：clean -> build -> archive

最后，打正式包，提交APP Store。

## archive注意点

* 上传app store的1024*1024尺寸的icon和屏幕截图不能加圆角，透明度，通道
* version，build版本号不能低于上次上传过的APP版本号
* info.plist 中获取权限的key对应的value必须要加说明，不能为空

## 审核被拒-修改回复
###  Background-location
```
Please revise your app to include features that require the persistent use of real-time location updates while the app is in the background. Please also add the following battery use disclaimer in your Application Description:
"Continued use of GPS running in the background can dramatically decrease battery life."

If your app does not require persistent real-time location updates, please remove the "location" setting from the UIBackgroundModes key. You may wish to use the significant-change location service or the region monitoring location service if persistent real-time location updates are not required for your app features.
```

翻译：请修改您的应用程序，以包括功能，需要持续使用的实时位置更新，而应用程序的背景。请在应用程序描述中添加以下电池使用免责声明：“继续使用在后台运行的全球定位系统，可以大大降低电池寿命。”
如果你的应用程序不需要持续的实时位置更新，请清除“位置”从uibackgroundmodes键设置。如果你的应用程序功能不需要持久的实时位置更新，您可能希望使用显着的更改位置服务或区域监测位置服务。

### Guideline 2.3.3 - Performance - Accurate Metadata
```
We noticed that your screenshots for iPhone 5.8” do not sufficiently reflect your app in use on the relevant device.

Please see attached screenshots for details.

Next Steps

To resolve this issue, please revise your screenshots to ensure that they accurately reflect the app in use on the supported devices. For iPhone, you need a set of 5.5-inch display screenshots and for iPad, you need a set for 12.9-inch display. This set will be scaled appropriately down to other device sizes when viewed on the App Store in each territory.

Resources

For resources on creating great screenshots for the App Store, you may want to review the App Store Product Page information available on the Apple developer portal.

Please ensure you have made any screenshot modifications using Media Manager. You should confirm your app looks and behaves identically in all languages and on all supported devices. Learn how to use Media Manager to add custom screenshots for each display size and localization.

Since your iTunes Connect status is Rejected, a new binary will be required. Make the desired metadata changes when you upload the new binary.

NOTE: Please be sure to make any metadata changes to all app localizations by selecting each specific localization and making appropriate changes.
```

回复模板如下：
```
尊敬的苹果审核委员会， 你好。
谢谢你的耐心和认真审核， 我们认真排查了你提到的7个审核条款， 我们对此回应如下：
1.1.6：我们App并没有包含误导用户的功能和虚假信息，全都是真实的。
2.3.0：我们App没有在通过苹果审核后修改任何的概念和功能。
2.3.1：我们App没有切换到任何赌博或彩票的网站，我们是原生App。
3.1.1：我们App内需要使用第三方支付来XXX。
4.3.0：我们App内容都是我们自己开发的， 并不存在抄袭或者大量相似。
5.2.1：我们App并没有法人实体和版权的风险。
5.3.4：我们App需要在XXX、XXX等服务中访问用户的位置信息。
这是我们经过认真自查之后，得到的结论， 我们的App需要尽快上线，因为我们有大量推广活动在做。
希望苹果审核团队能继续审核，感谢。
```

### Guideline 2.3.3 - Performance - Accurate Metadata
```
We noticed that your screenshots for iPhone 5.8” do not sufficiently reflect your app in use on the relevant device.

Please see attached screenshots for details.

Next Steps

To resolve this issue, please revise your screenshots to ensure that they accurately reflect the app in use on the supported devices. For iPhone, you need a set of 5.5-inch display screenshots and for iPad, you need a set for 12.9-inch display. This set will be scaled appropriately down to other device sizes when viewed on the App Store in each territory.

Resources

For resources on creating great screenshots for the App Store, you may want to review the App Store Product Page information available on the Apple developer portal.

Please ensure you have made any screenshot modifications using Media Manager. You should confirm your app looks and behaves identically in all languages and on all supported devices. Learn how to use Media Manager to add custom screenshots for each display size and localization.

Since your iTunes Connect status is Rejected, a new binary will be required. Make the desired metadata changes when you upload the new binary.

NOTE: Please be sure to make any metadata changes to all app localizations by selecting each specific localization and making appropriate changes.
```

`2.3 准确的元数据问题主要是应用标题、描述、截图等与应用功能严重不符`
* 原因：虽然针对iPhone X上传的设计图尺寸大小符合规定，但在设计图内容中出现的手机屏幕样式不是iPhoneX的样式。
* 解决：让设计修改设计图内容中手机屏幕的样式，修改成iPhoneX带刘海的样式，或者5.8寸预览图属于可选而非必选，所以，如果没有新的是配图可以忽略5.8寸不上传。

### Guideline 3.2.1 - Business - Other Business Model Issues - Acceptable
```
The seller and company names associated with your app do not reflect the financial institute name in the app or its metadata, as required by Guideline 3.2.1(viii) of the App Store Review Guidelines.

Next Steps

Your app must be published under a seller name and company name that reflects the financial institution. If you have developed this app on behalf of a client, please advise your client to add you to the development team of their Apple Developer account.

Please note that submitting documentation showing permission to publish this app on behalf of the financial institution will not resolve this issue. This app must be submitted under the financial institution’s own Apple Developer account.

Once created, you cannot change your seller name or company name in App Store Connect. For assistance with changing your company name or seller name, you will need to contact us. Select Membership and Account to request an update to your Apple Developer account information.

Please see attached screenshot for details.

Since your App Store Connect status is Rejected, a new binary will be required.
```

原因：
1. APP名称跟公司名字差距较大，APP没有表现出APP与公司的关系。
2. 开发者账号不是企业开发账号
3.  开发者申请的邮箱不是公司名字的邮箱（比如自己的QQ号码邮箱）
4. iTunes Connect中填写的技术支持网址不是跟公司有关的网址。
5.电商和金融理财类被拒的风险很大

解决：
1. 提供APP是其公司合法App的相关文件，向苹果提供公司营业执照和软著之类证明。在itunes connect中,app描述里面加上: "app名字"属于你们公司的。
2. 用公司帐号而非个人帐号提交应用。
3. 确认开发账号申请邮箱是否是公司邮箱
4. 技术支持是公司网站主页
5. 提供金融资质证明。审核期间app内部不能出现任何第三方的名字，屏蔽敏感页面和敏感词等审核通过后再打开。

### 解决方案中心回复提交附件注意事项（重要）
1. 上传的文件必须是png格式（直接修改后缀名不可以）
2. 并且文件大小限制在5M内
3. 上传文件的命名不要有汉字，要用字母

### Guideline 3.2.2 - Business - Other Business Model Issues - Unacceptable
```
The primary purpose of your app is to encourage users to watch ads or perform marketing-oriented tasks, which is not appropriate for the App Store.

Next Steps

We encourage you to review your app concept and incorporate different content and features that are in compliance with the App Store Review Guidelines.
```



### Guideline 5.1.1 - Legal - Privacy - Data Collection and Storage
```
We noticed that your app requests the user’s consent to access their camera but does not clarify the use of this feature in the permission modal alert.

Please see attached screenshot for details.

Next Steps

To resolve this issue, please revise the permission modal alert to specify why the app is requesting access to the user's camera.

Resources

To learn more about requesting the user’s permission to access app features, visit the iOS Human Interface Guidelines. You may also want to review the Technical Q&A QA1937: Resolving the Privacy-Sensitive Data App Rejection page for details on how to provide a usage description for permission request alerts.
```

* 原因：相机使用场景提示文本不够明确，需提示用户为何打开此功能。
* 解决：info.plist文件中，对应key为NSCameraUsageDescription的value说明在哪些地方要用到定位服务，用户的位置信息，例如："App会在XXX、XXX等服务中访问您的相机"。

### Guideline 5.1.1 - Legal - Privacy - Data Collection and Storage
```
Specific issue: We noticed that your app requires users to register with personal information to access non-account-based features.

Action required: To resolve this issue, please revise your app to let users freely access your app’s non account-based features. To protect users’ privacy, any registration feature that requires users to disclose personal information must be optional or tied to account-specific functionality. Additionally, the requested information must be relevant to the features.

More information: Apps cannot require user registration prior to allowing access to app content and features that are not associated specifically to the user. You should allow users to freely access your app’s non account-based features. For example, an e-commerce app should let users browse store offerings and other features that are not account-based before being asked to register, or a restaurant app should allow users to explore the menu before placing an order. Registration must then only be required for account-specific features, such as saving items for future reference or placing an order.
```

* 原因：在没有进行与用户信息相关的操作时，却强行让用户登录，甚至不登录就无法看到任何内容。
* 解决：增加游客模式。

### Guideline 5.1.5 - Legal - Privacy - Location Services
```
Your app uses location services but does not clarify the purpose of its use in the location modal alert. Apps that collect, transmit, or use location data must notify and obtain the user’s consent prior to accessing this data.

Please see attached screenshot for details.

Next Steps

To resolve this issue, please specify why the app is requesting the user's location in the location permission modal alert.

Resources

For additional information and instructions on configuring and presenting an alert, please review the Requesting Permission section of the iOS Human Interface Guidelines and the Information Property List Key Reference. You may also want to review the Technical Q&A QA1937: Resolving the Privacy-Sensitive Data App Rejection page for details on how to provide a usage description for permission request alerts.
```

* 原因：定位使用场景提示文本不够明确，需提示用户为何打开此功能。
* 解决：info.plist文件中，对应key为NSLocationWhenInUseUsageDescription的value说明在哪些地方要用到定位服务，用户的位置信息，例如："App会在XXX、XXX等服务中访问您的位置信息"。

## iOS的各种结果文件：
（1）app
iOS编译以后生成的原始文件，实际是一个文件夹，里面包含各种资源文件（图片，第三方bundle，plist等文件），程序的可执行文件(二进制格式)以及对所有文件的签名记录（_CodeSignature）
不能上传AppStore

（2）dSYM
生成.app时的附属产物。本质是一个文件夹，其中只有一个最大的文件，作用是对iOS程序闪退后产生的log文件进行符号化（desymbolicate）；通俗的说，就是把无意义的内存地址变成可读的程序中的类和方法以及代码行数
不能上传AppStore

（3）ipa
实际上就是把.app放到Payload文件夹后，对Payload进行了zip操作，最后改了下扩展名。
可通过Application Loader上传AppStore

（4）xcarchive
实际上也是一个文件夹，包含.ipa和.dSYM文件
可通过Xcode上传AppStore

## 获取APP在APP Store的最新版本号
```
//版本更新方法实现
- (NSComparisonResult)shouldUpdateOnlineVersion {
    //获取app地址
    NSURL *appURLPath = [NSURL URLWithString:[NSString stringWithFormat:@"http://itunes.apple.com/lookup?id=%@",ITUNESAPPID]];
    //获取发布版本的Version
    NSString *string = [NSString stringWithContentsOfURL:appURLPath encoding:NSUTF8StringEncoding error:nil];

    NSString *onlineVersion;

    if (!([string isEqualToString:@""] || string == nil)) {
    NSDictionary *dic = [JsonUtil dictionaryWithJsonString:string];

        NSInteger resultCount = [[dic objectForKey:@"resultCount"] integerValue];
        if (resultCount == 1) {
            NSArray *result = [dic objectForKey:@"results"];
            NSDictionary *first = [result firstObject];
            onlineVersion = [first objectForKey:@"version"];
        }else{
            return NSOrderedDescending;
        }
    }

    NSString *nowVersion = [[[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleShortVersionString"];

    NSComparisonResult result = [nowVersion compare:onlineVersion];
    return result;
    //    NSOrderedAscending 当前版本低于(伪)线上版本
    //    NSOrderedSame 等于
    //    NSOrderedDescending 当前版本高于(伪)线上版本
    //  ！！该接口不一定能获取到最新appstore上的版本信息
}
```
