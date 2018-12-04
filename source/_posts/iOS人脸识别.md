---
title: iOS人脸识别
copyright: true
date: 2018-11-29 11:19:34
categories: 笔记
tags: [人脸识别]
toc:
---

## 人脸识别的几种实现方式
### GPU IMAGE

### OPENCV 
<!--more-->

### Vision
```
- (void)beginRecognize{
    __block NSError *error;
    // 创建处理requestHandler
    VNImageRequestHandler *handler = [[VNImageRequestHandler alloc] initWithCIImage:[[CIImage alloc] initWithImage:image] options:@{}];
    VNImageBasedRequest *request = [[VNImageBasedRequest alloc] init];
    request = [[VNDetectFaceLandmarksRequest alloc] initWithCompletionHandler:^(VNRequest * _Nonnull request, NSError * _Nullable error) {
        NSArray *vnobservations = request.results;

        if (vnobservations.count<=0) {

        UIAlertController *alertContrller = [UIAlertController alertControllerWithTitle:@"No face detected!" message:@"Sorry, we are NOT able to detect any face in this photo!" preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *confirmAction = [UIAlertAction actionWithTitle:@"Confirm" style:UIAlertActionStyleDefault handler:^(UIAlertAction * _Nonnull action) {
        
        }];
        [alertContrller addAction:confirmAction];
        [self presentViewController:alertContrller animated:YES completion:nil];

        }else{
            //...
        }

    }];

    request.preferBackgroundProcessing = YES;
    // 发送识别请求 在后台执行 在更新UI的时候记得切换到主线程
    //    dispatch_async(dispatch_get_main_queue(), ^{
    //        [handler performRequests:@[requset] error:&error];
    //    });
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        [handler performRequests:@[request] error:&error];
    });

    if (error) {
    NSLog(@"%@",error.localizedDescription);
    }
}
```

### 三方
讯飞，腾讯，百度，七牛云，美摄，Amazon recognition,dlib
