---
title: IAP内购实现
copyright: true
date: 2018-11-30 15:00:12
categories: 笔记
tags: [IAP]
toc:
---

## IAP
* IAP：In-App-Purchase 应用内购买

* 苹果的内购分为以下四类商品：
1. 消耗型项目
2. 非消耗型项目
3. 自动续期订阅
4. 非续期订阅

 <!--more-->
* 恢复购买
 在如下两种情况下，针对自动订阅或者非消耗性商品，需要使用恢复购买功能：
 1.在消费者拥有的其他设备上安装它们。
 2.在相关应用程序被删除的设备上重新安装它们。

## 开始内购前的配置项
* iTunesconnect后台配置内购项目
在“我的APP”——“功能”——“App内购买项目”添加适合自己的商品
* Xcode工程配置：Capabilities -> In-App Purchase -> ON

## 实现
### 原生
加入头文件：`#import <StoreKit/StoreKit.h>`
添加代理：`<SKPaymentTransactionObserver,SKProductsRequestDelegate>`
```

-(void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:animated];

    [[SKPaymentQueue defaultQueue] addTransactionObserver:self];
}

-(void)viewWillDisappear:(BOOL)animated{
    [super viewWillDisappear:animated];

    [[SKPaymentQueue defaultQueue] removeTransactionObserver:self];
}

-(void)inAppPurchase{
    if([SKPaymentQueue canMakePayments]){
        [self requestProductData:product];
    }else{
        NSLog(@"不允许程序内付费");
    }
}

//请求商品
- (void)requestProductData:(NSString *)type{
    NSLog(@"-------------请求对应的产品信息----------------");

    [SVProgressHUD showWithStatus:nil maskType:SVProgressHUDMaskTypeBlack];

    NSArray *product = [[NSArray alloc] initWithObjects:type,nil];

    NSSet *nsset = [NSSet setWithArray:product];
    SKProductsRequest *request = [[SKProductsRequest alloc] initWithProductIdentifiers:nsset];
    request.delegate = self;
    [request start];

}

//收到产品返回信息
- (void)productsRequest:(SKProductsRequest *)request didReceiveResponse:(SKProductsResponse *)response{

    NSLog(@"--------------收到产品反馈消息---------------------");
    NSArray *product = response.products;
    if([product count] == 0){
        [SVProgressHUD dismiss];
        NSLog(@"--------------没有商品------------------");
        return;
    }

    NSLog(@"productID:%@", response.invalidProductIdentifiers);
    NSLog(@"产品付费数量:%lu",(unsigned long)[product count]);

    SKProduct *p = nil;
    for (SKProduct *pro in product) {
        NSLog(@"%@", [pro description]);
        NSLog(@"%@", [pro localizedTitle]);
        NSLog(@"%@", [pro localizedDescription]);
        NSLog(@"%@", [pro price]);
        NSLog(@"%@", [pro productIdentifier]);

        if([pro.productIdentifier isEqualToString:_currentProId]){
            p = pro;
        }
    }

    SKPayment *payment = [SKPayment paymentWithProduct:p];

    NSLog(@"发送购买请求");
    [[SKPaymentQueue defaultQueue] addPayment:payment];
}

//请求失败
- (void)request:(SKRequest *)request didFailWithError:(NSError *)error{
    [SVProgressHUD showErrorWithStatus:@"支付失败"];
    NSLog(@"------------------错误-----------------:%@", error);
}

- (void)requestDidFinish:(SKRequest *)request{
    [SVProgressHUD dismiss];
    NSLog(@"------------反馈信息结束-----------------");
}
//沙盒测试环境验证
#define SANDBOX @"https://sandbox.itunes.apple.com/verifyReceipt"
//正式环境验证
#define AppStore @"https://buy.itunes.apple.com/verifyReceipt"
/**
*  验证购买，避免越狱软件模拟苹果请求达到非法购买问题
*
*/
-(void)verifyPurchaseWithPaymentTransaction{
    //从沙盒中获取交易凭证并且拼接成请求体数据
    NSURL *receiptUrl=[[NSBundle mainBundle] appStoreReceiptURL];
    NSData *receiptData=[NSData dataWithContentsOfURL:receiptUrl];

    NSString *receiptString=[receiptData base64EncodedStringWithOptions:NSDataBase64EncodingEndLineWithLineFeed];//转化为base64字符串

    NSString *bodyString = [NSString stringWithFormat:@"{\\"receipt-data\\" : \\"%@\\"}", receiptString];//拼接请求数据
    NSData *bodyData = [bodyString dataUsingEncoding:NSUTF8StringEncoding];


    //创建请求到苹果官方进行购买验证
    NSURL *url=[NSURL URLWithString:SANDBOX];
    NSMutableURLRequest *requestM=[NSMutableURLRequest requestWithURL:url];
    requestM.HTTPBody=bodyData;
    requestM.HTTPMethod=@"POST";
    //创建连接并发送同步请求
    NSError *error=nil;
    NSData *responseData=[NSURLConnection sendSynchronousRequest:requestM returningResponse:nil error:&error];
    if (error) {
        NSLog(@"验证购买过程中发生错误，错误信息：%@",error.localizedDescription);
        return;
    }
    NSDictionary *dic=[NSJSONSerialization JSONObjectWithData:responseData options:NSJSONReadingAllowFragments error:nil];
    NSLog(@"%@",dic);
    if([dic[@"status"] intValue]==0){
        NSLog(@"购买成功！");
        NSDictionary *dicReceipt= dic[@"receipt"];
        NSDictionary *dicInApp=[dicReceipt[@"in_app"] firstObject];
        NSString *productIdentifier= dicInApp[@"product_id"];//读取产品标识
        //如果是消耗品则记录购买数量，非消耗品则记录是否购买过
        NSUserDefaults *defaults=[NSUserDefaults standardUserDefaults];
        if ([productIdentifier isEqualToString:@"123"]) {
            int purchasedCount=[defaults integerForKey:productIdentifier];//已购买数量
            [[NSUserDefaults standardUserDefaults] setInteger:(purchasedCount+1) forKey:productIdentifier];
        }else{
            [defaults setBool:YES forKey:productIdentifier];
        }
    //在此处对购买记录进行存储，可以存储到开发商的服务器端
    }else{
        NSLog(@"购买失败，未通过验证！");
    }
}
//监听购买结果
- (void)paymentQueue:(SKPaymentQueue *)queue updatedTransactions:(NSArray *)transaction{

    for(SKPaymentTransaction *tran in transaction){

        switch (tran.transactionState) {
            case SKPaymentTransactionStatePurchased:{
            NSLog(@"交易完成");
            [self verifyPurchaseWithPaymentTransaction];
            [[SKPaymentQueue defaultQueue] finishTransaction:tran];
        }
            break;
        case SKPaymentTransactionStatePurchasing:
            NSLog(@"商品添加进列表");
            break;
        case SKPaymentTransactionStateRestored:{
            NSLog(@"已经购买过商品");
            [[SKPaymentQueue defaultQueue] finishTransaction:tran];
        }
            break;
        case SKPaymentTransactionStateFailed:{
            NSLog(@"交易失败");
            [[SKPaymentQueue defaultQueue] finishTransaction:tran];
            [SVProgressHUD showErrorWithStatus:@"购买失败"];
        }
            break;
        default:
            break;
        }
    }
}

//交易结束
- (void)completeTransaction:(SKPaymentTransaction *)transaction{
NSLog(@"交易结束");

    [[SKPaymentQueue defaultQueue] finishTransaction:transaction];
}
```

### 第三方库 XYIAPKit
* 购买
```
#pragma mark - IAP
-(void)inAppPurchase{

    if (![XYStore canMakePayments]) {
        [ProgressHUD showTextTipMsg:@"This device is not able or allowed to make payments"];
    }else{
        self.HUD = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
        //1、查询在线商品
        NSSet* dataSet = [[NSSet alloc] initWithObjects:IAPProductID, nil];
        [[XYStore defaultStore]requestProducts:dataSet success:^(NSArray *products, NSArray *invalidProductIdentifiers) {

            if (products.count>0) {
            //2、添加购买
            BOOL flag = NO;
            for (SKProduct *sKProduct in products) {

                if([sKProduct.productIdentifier isEqualToString: IAPProductID]){
                    flag = YES;
                    [XYStoreiTunesReceiptVerifier shareInstance].sharedSecretKey = IAPSharedKey;
                    [[XYStore defaultStore] addPayment:IAPProductID
                    success:^(SKPaymentTransaction *transaction)
                    {
                        if ([[XYStoreiTunesReceiptVerifier shareInstance]isSubscribedWithAutoRenewProduct:IAPProductID]) {
                            //追踪收入
                            [self.HUD hideAnimated:YES];
                            [self tryForFree];
                        }else{
                            [self.HUD hideAnimated:NO];
                            [ProgressHUD showTextTipMsg:@"Verification Failed"];
                        }

                    } failure:^(SKPaymentTransaction *transaction, NSError *error) {
                        /*
                        内购验证凭据返回结果状态码说明
                        21000 App Store无法读取你提供的JSON数据
                        21002 收据数据不符合格式
                        21003 收据无法被验证
                        21004 你提供的共享密钥和账户的共享密钥不一致
                        21005 收据服务器当前不可用
                        21006 收据是有效的，但订阅服务已经过期。当收到这个信息时，解码后的收据信息也包含在返回内容中
                        21007 收据信息是测试用（sandbox），但却被发送到产品环境中验证
                        21008 收据信息是产品环境中使用，但却被发送到测试环境中验证
                        */
                        [self.HUD hideAnimated:NO];
                        if (transaction.error.code == SKErrorPaymentCancelled) {
                            [ProgressHUD showTextTipMsg:@"Purchase Cancel"];
                        }else{
                            [ProgressHUD showTextTipMsg:transaction.error.localizedDescription];
                        }
                    }];
                    break;

                }else{
                    //NSLog(@"不相同");
                }
            }
            if (!flag) {
                [self.HUD hideAnimated:NO];
            }
            }else{
                [self.HUD hideAnimated:NO];
                [ProgressHUD showTextTipMsg:@"Failed to get product information"];
            }
        } failure:^(NSError *error) {
            [self.HUD hideAnimated:NO];
            [ProgressHUD showTextTipMsg:@"Failed to get product information"];
        }];
    }
}
```

* 恢复购买
```
#pragma mark 恢复购买(主要是针对非消耗产品，自动订阅)
- (void)restoreClick{

    self.HUD = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
    [[XYStore defaultStore] restoreTransactionsOnSuccess:^(NSArray *transactions) {

        [self.HUD hideAnimated:NO];
        for (SKPaymentTransaction *transaction in transactions)
        {
            NSString *purchased = transaction.payment.productIdentifier;
            if([purchased isEqualToString:IAPProductID])
            {
                if ([[XYStoreiTunesReceiptVerifier shareInstance]isSubscribedWithAutoRenewProduct:IAPProductID]) {
                    [ProgressHUD showTextTipMsg:@"Verification Succeed"];
                }else{
                    [ProgressHUD showTextTipMsg:@"Verification Failed"];
                }
                break;
            }
        }

    } failure:^(NSError *error) {
        [self.HUD hideAnimated:NO];
        [ProgressHUD showTextTipMsg:error.localizedDescription];
    }];
}
```

## 沙箱测试
### 添加沙盒测试员
非正式环境下测试需要添加沙盒测试员

### 生成共享密钥
 进入 Features，点击 In-App Purchases 表格左上角的 View Share Secret。有可能，你还需要点击 Generate Shared Secret 按钮。
 
### 注意事项
* 退出自己的账号，用沙盒测试账号登录

* 沙箱环境自动续费时间缩短了，1周 对应 3分钟，1个月 对应 5分钟，2个月 对应 10分钟，3个月 对应 15分钟，6个月 对应 30分钟，一年 对应 1小时。

* 购买完一个一周 类型订阅，不要在APP不退出的情况等待了，必须3分钟 或是 10分钟后重新登录，Apple才会主动告知你结果。
 
* 沙箱环境自动续费是不一定的，要多测几个账号。
 
 
 参考文章：
 [iOS内购编程指南](https://www.jianshu.com/p/17e0d11149f3)
 [IAP开发的那些坑](https://www.jianshu.com/p/ebdeea271352)
 [获取共享密钥](https://blog.csdn.net/kmyhy/article/details/75489241)
 [iOS开发之内购-AppStore](https://yimouleng.com/2015/12/17/ios-AppStore/)
