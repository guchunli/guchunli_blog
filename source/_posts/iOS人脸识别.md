---
title: iOS人脸识别
copyright: true
date: 2018-11-29 11:19:34
categories: 笔记
tags: [人脸识别]
toc:
---

## 人脸识别的几种实现方式
### Core Image 静态识别
### AVFoundation 动态识别
[CoreImage和AVFoundation Demo](https://github.com/guchunli/FaceDetectorDemo)

<!--more-->
### OPENCV 
OpenCV 是一个 C++ 的 API。

#### 集成 OpenCV 的常见错误
* 报错1：'opencv2/highgui/cap_ios.h' file not found
解决：其在3.2.0版中已经移到videoio文件夹下，所以需要把 `#import <opencv2/highgui/cap_ios.h>` 改为 `#import <opencv2/videoio/cap_ios.h>`。

* 报错2：enum { NO, GAIN, GAIN_BLOCKS };    Expected identifier
只要把NO修改成 NO_EXPOSURE_COMPENSATOR 或 NO_EXPOSURE_COMPENSATOR = 0
```
//    enum { NO, GAIN, GAIN_BLOCKS };
enum { NO_EXPOSURE_COMPENSATOR = 0, GAIN, GAIN_BLOCKS };
```

* 设置OC与C++混合编译：Build Settings -> Compile Source As：Objective-C++。

[OpenCV Demo](https://github.com/guchunli/OpenCVFaceDemo)


### Vision
Apple 在 WWDC 2017 伴随iOS 11推出的基于CoreML的图像识别框架。
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

### 第三方
讯飞，腾讯，百度，七牛云，美摄，Amazon recognition,dlib，Face++。
