---
title: Apple Pay学习笔记
date: 2017-03-15 16:37:17
categories: 笔记
tags: 
---
## 概述
### 1.支付方式：Touch ID/ Passcode
### 2.设备要求：iPhone6以上（iphone:线上/线下 ipad:线上 watch:线下）
### 3.系统要求：iOS8.0以上，银联（中国）：9.2
<!--more-->
### 4.基于NFC
### 5.2014年10月20日在美国正式上线，2016年2月18日凌晨5：00， Apple Pay 业务在中国上线。
### 6.与微信支付以及支付宝等第三方支付平台的区别：
* 硬件方面
    + Apple Pay：必须是iOS设备， 而且是按照线上支付和线下支付区分不同的真机设备
    + 微信、支付宝： 基本跟硬件设备无关， 支持大多数的智能手机
* 网络环境要求
    + Apple Pay：线上支付需要联网， 线下支付无需联网就可以支付
    + 微信、支付宝： 无论是线上还是线下支付， 都需要联网使用
* 使用技术
    + Apple Pay：线下支付使用的是 基于NFC的近场通讯技术
    + 微信、支付宝： 线下支付使用的是 扫码支付（条形码、二维码）
* 主要功能
    + Apple Pay：线上支付、线下支付、部分升级后的ATM机可以取款
    + 微信、支付宝： 线上支付、线下支付、转账、理财等  
* 安全性能
    + Apple Pay：不保留银行卡信息，并且不会暴漏给外界、不分流银行存款（不需要从银行卡转钱到另外一个平台）、不能充值  安全性较高
    + 微信、支付宝： 密码保护，身份验证等手段保护账户  安全性相对稍差
* 支付时长
    + Apple Pay：无论是线上支付，还是线下支付， 只需要验证指纹即可支付。非常迅速
    + 微信、支付宝： 需要扫码支付， 流程相对繁琐，所以时长较长
* 各自弊端
    + Apple Pay：只适用于苹果设备， 支付场景单一，无转账理财等业务
    + 微信、支付宝： 安全性较差， 必须联网操作，需要充值到对应平台

## 注册并配置一个商业标示符
1.添加一个App ID
2.配置Merchant ID
3.为Merchant ID 配置证书, 并下载证书安装到钥匙串
4.检查安装到钥匙串中的证书是否有效
5.绑定Merchant ID 到 APP ID

证书失效，请到以下地址下载重新安装：https://www.apple.com/certificateauthority/
![证书](/assets/20170315_ApplePay/applepay.png)

## 代码实现

```
//一.判断当前设备是否支持支付

if (![PKPaymentAuthorizationViewController canMakePayments]) {

NSLog(@"该设备不支持ApplePay");

return;



//二.判断Wallet有没有添加该支付网络的储蓄卡/信用卡

}else if ([PKPaymentAuthorizationViewController canMakePaymentsUsingNetworks:@[PKPaymentNetworkChinaUnionPay,PKPaymentNetworkVisa]]){

NSLog(@"Wallet没有添加该支付网络的储蓄卡/信用卡");

//创建一个添加卡的按钮

PKPaymentButton *btn = [PKPaymentButton buttonWithType:PKPaymentButtonTypeSetUp style:PKPaymentButtonStyleBlack];

[btn addTarget:self action:@selector(jumpToSetup) forControlEvents:UIControlEventTouchUpInside];

[self.payView addSubview:btn];        

}else{

//创建一个支付按钮

PKPaymentButton *btn = [PKPaymentButton buttonWithType:PKPaymentButtonTypeBuy style:PKPaymentButtonStyleWhiteOutline];

[btn addTarget:self action:@selector(jumpToPay) forControlEvents:UIControlEventTouchUpInside];

[self.payView addSubview:btn];

}



- (void)jumpToSetup{



NSLog(@"跳转到添加银行卡页面");



PKPassLibrary * pk = [[PKPassLibrary alloc]init];

[pk openPaymentSetup];

}



- (void)jumpToPay{



//三.创建支付请求并配置各项信息



//1.创建支付请求

PKPaymentRequest *request = [[PKPaymentRequest alloc]init];



//2.配置参数

//2.1 商店标识

request.merchantIdentifier = @"";

//2.2 国家代码

request.countryCode = @"CN";

//2.3 货币代码

request.currencyCode = @"CNY";

//2.4 支持的支付网络

request.supportedNetworks = @[PKPaymentNetworkChinaUnionPay,PKPaymentNetworkVisa];

//2.5 支付请求包含一个支付摘要项目的列表

NSDecimalNumber *price1 = [NSDecimalNumber decimalNumberWithString:@"10.0"];

PKPaymentSummaryItem *item1 = [PKPaymentSummaryItem summaryItemWithLabel:@"iPhone" amount:price1];



NSDecimalNumber *price2 = [NSDecimalNumber decimalNumberWithString:@"20.0"];

PKPaymentSummaryItem *item2 = [PKPaymentSummaryItem summaryItemWithLabel:@"iPad" amount:price2 type:PKPaymentSummaryItemTypePending];



NSDecimalNumber *totalPrice = [NSDecimalNumber zero];

totalPrice = [totalPrice decimalNumberByAdding:price1];

totalPrice = [totalPrice decimalNumberByAdding:price2];

PKPaymentSummaryItem *total = [PKPaymentSummaryItem summaryItemWithLabel:@"财务中心" amount:totalPrice type:PKPaymentSummaryItemTypePending];

//注意：数组最后一个是总价格

request.paymentSummaryItems = @[item1,item2,total];

//2.6运输方式

NSDecimalNumber *shippingPrice = [NSDecimalNumber decimalNumberWithString:@"18.0"];

PKShippingMethod *method = [PKShippingMethod summaryItemWithLabel:@"顺风快递" amount:shippingPrice];

method.identifier = @"shunfeng";

method.detail = @"24小时内送到";

request.shippingMethods = @[method];

request.shippingType = PKShippingTypeServicePickup;

//2.7通过指定merchantCapabilities属性来指定你支持的支付处理标准，3DS支付方式是必须支持的，EMV方式是可选的

request.merchantCapabilities = PKMerchantCapability3DS | PKMerchantCapabilityEMV | PKMerchantCapabilityCredit | PKMerchantCapabilityDebit;

//2.8需要的配送信息和账单信息

request.requiredBillingAddressFields = PKAddressFieldAll;

request.requiredShippingAddressFields = PKAddressFieldAll;



//2.9 存储额外信息

request.applicationData = [@"购物车ID：123456" dataUsingEncoding:NSUTF8StringEncoding];



//四.弹出授权控制器，让用户给支付授权

//开始支付

PKPaymentAuthorizationViewController *pkCtrl = [[PKPaymentAuthorizationViewController alloc]initWithPaymentRequest:request];

if (pkCtrl == nil) {

NSLog(@"授权控制器创建失败");

return;

}

pkCtrl.delegate = self;

[self presentViewController:pkCtrl animated:YES completion:nil];

}



#pragma mark - PKPaymentAuthorizationViewControllerDelegate

//五.处理支付凭证

//授权成功

- (void)paymentAuthorizationViewController:(PKPaymentAuthorizationViewController *)controller

didAuthorizePayment:(PKPayment *)payment

completion:(void (^)(PKPaymentAuthorizationStatus status))completion{



/*

服务器接收到token后的一般处理流程:

1.验证支付数据的哈希表和签名

2.为加密过的支付数据解码

3.向支付处理系统提交支付数据

4.向订单追踪系统提交订单

*/

NSLog(@"验证授权---%@",payment.token);

/*

处理支付请求时，你有两个选择:

1.你既可以利用支付平台处理支付请求，

2.也可以自己实现支付请求处理流程。

一个常用的支付平台可以完成上述大部分操作。

*/

NSLog(@"验证通过后，需要开发中继续完成交易");

BOOL isSuccess = YES;

if (isSuccess) {

completion(PKPaymentAuthorizationStatusSuccess);

}else{

completion(PKPaymentAuthorizationStatusFailure);

}



}

//六.关闭授权控制器

//授权成功或者取消授权之后会调用此方法

- (void)paymentAuthorizationViewControllerDidFinish:(PKPaymentAuthorizationViewController *)controller{



NSLog(@"取消或者交易完成");

[self dismissViewControllerAnimated:YES completion:nil];

}
```


参考文章：
[Apple Pay--iOS开发](http://www.cnblogs.com/dashunzi/archive/2016/02/23/ApplePay.html#undefined)



