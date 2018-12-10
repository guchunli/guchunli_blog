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
共享密钥：是一个32位随机生成的字母数字字符串，在联系苹果服务器获取APP内购买项目收据时使用的唯一代码。没有共享密钥，将无法再沙箱技术模式下测试自动续订APP内购买项目。另外，共享密钥不能在 APP Store 使用。
 生成共享密钥：进入 Features，点击 In-App Purchases 表格左上角的 View Share Secret。有可能，你还需要点击 Generate Shared Secret 按钮。
 当传递用户 receipt-data 和 shared key 时，会返回一个包含用户购买历史的json数据，里面包含了订阅的详细信息：
 ```
 {
     "status": 0,
     "environment": "Sandbox",
     "receipt": {
     "receipt_type": "ProductionSandbox",
     "adam_id": 0,
     "app_item_id": 0,
     "bundle_id": "xxx",
     "application_version": "101",
     "download_id": 0,
     "version_external_identifier": 0,
     "receipt_creation_date": "2018-11-29 08:30:30 Etc/GMT",
     "receipt_creation_date_ms": "1543480230000",
     "receipt_creation_date_pst": "2018-11-29 00:30:30 America/Los_Angeles",
     "request_date": "2018-11-29 08:30:50 Etc/GMT",
     "request_date_ms": "1543480250216",
     "request_date_pst": "2018-11-29 00:30:50 America/Los_Angeles",
     "original_purchase_date": "2013-08-01 07:00:00 Etc/GMT",
     "original_purchase_date_ms": "1375340400000",
     "original_purchase_date_pst": "2013-08-01 00:00:00 America/Los_Angeles",
     "original_application_version": "1.0",
     "in_app": [{
         "quantity": "1",
         "product_id": "product_id_xxx",
         "transaction_id": "1000000479728157",
         "original_transaction_id": "1000000479726062",
         "purchase_date": "2018-11-29 08:29:26 Etc/GMT",
         "purchase_date_ms": "1543480166000",
         "purchase_date_pst": "2018-11-29 00:29:26 America/Los_Angeles",
         "original_purchase_date": "2018-11-29 08:27:26 Etc/GMT",
         "original_purchase_date_ms": "1543480046000",
         "original_purchase_date_pst": "2018-11-29 00:27:26 America/Los_Angeles",
         "expires_date": "2018-11-29 09:29:26 Etc/GMT",
         "expires_date_ms": "1543483766000",
         "expires_date_pst": "2018-11-29 01:29:26 America/Los_Angeles",
         "web_order_line_item_id": "1000000041543390",
         "is_trial_period": "false",
         "is_in_intro_offer_period": "false"
     }]
     },
     "latest_receipt_info": [{
         "quantity": "1",
         "product_id": "product_id_xxx",
         "transaction_id": "1000000479726062",
         "original_transaction_id": "1000000479726062",
         "purchase_date": "2018-11-29 08:27:26 Etc/GMT",
         "purchase_date_ms": "1543480046000",
         "purchase_date_pst": "2018-11-29 00:27:26 America/Los_Angeles",
         "original_purchase_date": "2018-11-29 08:27:26 Etc/GMT",
         "original_purchase_date_ms": "1543480046000",
         "original_purchase_date_pst": "2018-11-29 00:27:26 America/Los_Angeles",
         "expires_date": "2018-11-29 08:29:26 Etc/GMT",
         "expires_date_ms": "1543480166000",
         "expires_date_pst": "2018-11-29 00:29:26 America/Los_Angeles",
         "web_order_line_item_id": "1000000041543389",
         "is_trial_period": "true",
         "is_in_intro_offer_period": "false"
     }],
     "latest_receipt": "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",
     "pending_renewal_info": [{
         "auto_renew_product_id": "product_id_xxx",
         "original_transaction_id": "1000000479726062",
         "product_id": "product_id_xxx",
         "auto_renew_status": "1"
     }]
 }
 ```
 
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
