---
title: iOS人脸识别
copyright: true
date: 2018-11-29 11:19:34
categories: 笔记
tags: [人脸识别]
toc:
---

## 人脸识别的几种实现方式
### GPU IMAGE 静态识别

### AVFoundation 动态识别

### OPENCV 
<!--more-->
OpenCV 是一个 C++ 的 API，因此不能直接在 Swift 和 Objective-C 代码中使用，但能在 Objective-C++ 文件中使用。
1.Objective-C++ 是 Objective-C 和 C++ 的混合物，让你可以在 Objective-C 类中使用 C++ 对象。clang 编译器会把所有后缀名为 .mm 的文件都当做是 Objective-C++。一般来说，它会如你所期望的那样运行，但还是有一些使用 Objective-C++ 的注意事项。内存管理是你最应该格外注意的点，因为 ARC 只对 Objective-C 对象有效。当你使用一个 C++ 对象作为类属性的时候，其唯一有效的属性就是 assign。因此，你的 dealloc 函数应确保 C++ 对象被正确地释放了。
2.如果你在 Objective-C++ 头文件中引入了 C++ 头文件，当你在工程中使用该 Objective-C++ 文件的时候就泄露了 C++ 的依赖。任何引入你的 Objective-C++ 类的 Objective-C 类也会引入该 C++ 类，因此该 Objective-C 文件也要被声明为 Objective-C++ 的文件。这会像森林大火一样在工程中迅速蔓延。所以，应该把你引入 C++ 文件的地方都用 #ifdef __cplusplus 包起来，并且只要可能，就尽量只在 .mm实现文件中引入 C++ 头文件。

#### 集成 OpenCV 的常见错误
* 报错1：'opencv2/highgui/cap_ios.h' file not found
解决：其在3.2.0版中已经移到videoio文件夹下，所以需要把 `#import <opencv2/highgui/cap_ios.h>` 改为 `#import <opencv2/videoio/cap_ios.h>`。

* 报错2：enum { NO, GAIN, GAIN_BLOCKS };    Expected identifier
只要把NO修改成 NO_EXPOSURE_COMPENSATOR 或 NO_EXPOSURE_COMPENSATOR = 0
```
//    enum { NO, GAIN, GAIN_BLOCKS };
enum { NO_EXPOSURE_COMPENSATOR = 0, GAIN, GAIN_BLOCKS };
```

#### 编译 OpenCV framework


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
