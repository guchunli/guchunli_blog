---
title: iOS中操作PDF文件
copyright: true
date: 2018-12-10 15:12:20
categories: 笔记
tags: [PDF]
toc:
---


## 加载PDF文件
### UIWebView 加载本地或网络文件
<!--more-->
```
#import <WebKit/WebKit.h>
- (void)loadPDFFile{

    //初始化myWebView
    WKWebView *myWebView = [[WKWebView alloc] initWithFrame:self.view.bounds];
    myWebView.backgroundColor = [UIColor whiteColor];
    //本地文件
    //NSURL *filePath = [NSURL fileURLWithPath:[[NSBundle mainBundle] pathForResource:@"photos" ofType:@"pdf"]];
    //网络文件
    NSURL *filePath = [NSURL URLWithString:@"https://xxx.pdf"];
    NSURLRequest *request = [NSURLRequest requestWithURL: filePath];
    [myWebView loadRequest:request];
    [self.view addSubview:myWebView];
}
```

### QLPreviewController
```
#import <QuickLook/QuickLook.h>
<QLPreviewControllerDataSource,QLPreviewControllerDelegate>

- (void)loadPDFFile{
    QLPreviewController *QLPVC = [[QLPreviewController alloc] init];
    QLPVC.dataSource = self;
    QLPVC.delegate = self;
    [self presentViewController:QLPVC animated:YES completion:nil];
}
#pragma mark QLPreviewControllerDataSource
/*
*所要加载pdf文档的个数
*/
- (NSInteger)numberOfPreviewItemsInPreviewController:(QLPreviewController *)controller{
return 1;
}

/*
* 返回每个index pdf文档所对应的文档路径
*/
- (id<QLPreviewItem>)previewController:(QLPreviewController *)controller previewItemAtIndex:(NSInteger)index{
//    NSArray *arr = @[FILE_PATH1,FILE_PATH2];
//    return [NSURL fileURLWithPath:arr[index]];
    NSString *path = [[NSBundle mainBundle] pathForResource:@"photos" ofType:@"pdf"];
    return [NSURL fileURLWithPath:path];
}
```

### 第三方框架vfr/Reader加载pdf文档
github地址：[vfr/Reader](https://github.com/vfr/Reader.git)
```
#import "ReaderViewController.h"
<ReaderViewControllerDelegate>

- (void)loadPDFFile{

    NSString *path = [[NSBundle mainBundle] pathForResource:@"photos" ofType:@"pdf"];
    ReaderDocument *doc = [[ReaderDocument alloc] initWithFilePath:path password:nil];
    ReaderViewController *rvc = [[ReaderViewController alloc] initWithReaderDocument:doc];
    rvc.delegate = self;
    [self presentViewController:rvc animated:YES completion:nil];
}

#pragma mark - ReaderViewControllerDelegate
- (void)dismissReaderViewController:(ReaderViewController *)viewController {
[self dismissViewControllerAnimated:YES completion:nil];
}
```

## 下载PDF文件
### data writeToFile
```
- (void)downloadPDFFile{

    NSString *filePath = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]stringByAppendingPathComponent:@"test.pdf"];
    NSLog(@"下载到：%@",filePath);

    NSString *pdfPath = @"https://xxx.pdf";
    NSData *data = [NSData dataWithContentsOfURL:[NSURL URLWithString:pdfPath]];

    BOOL isWrite = [data writeToFile:filePath atomically:YES];
    if (isWrite) {
    NSLog(@"保存成功");
    } else {
    NSLog(@"写入失败");
    }
}
```

### AFNetworking
```
- (void)downloadPDFFile{

    NSString *filePath = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]stringByAppendingPathComponent:@"test.pdf"];
    NSLog(@"下载到：%@",filePath);

    NSString *pdfPath = @"https://xxx.pdf";
    //创建 Request
    NSURLRequest* request = [NSURLRequest requestWithURL:[NSURL URLWithString:pdfPath]];

    //下载进行中的事件
    [SVProgressHUD show];
    /* 开始请求下载 */
    AFURLSessionManager *manager = [[AFURLSessionManager alloc] initWithSessionConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]];
    NSURLSessionDownloadTask *downloadTask = [manager downloadTaskWithRequest:request progress:^(NSProgress * _Nonnull downloadProgress) {
    NSLog(@"下载进度：%.0f％", downloadProgress.fractionCompleted * 100);
    } destination:^NSURL * _Nonnull(NSURL * _Nonnull targetPath, NSURLResponse * _Nonnull response) {
        dispatch_async(dispatch_get_main_queue(), ^{
        //如果需要进行UI操作，需要获取主线程进行操作
        });
        /* 设定下载到的位置 */
        return [NSURL fileURLWithPath:filePath];

    } completionHandler:^(NSURLResponse * _Nonnull response, NSURL * _Nullable filePath, NSError * _Nullable error) {
        NSLog(@"下载完成");
        [SVProgressHUD dismiss];
    }];
    [downloadTask resume];
    }
```

## 生成PDF文件
### 各种格式文件生成PDF文件或生成图片文件
通过WebView可以加载各种格式文件，然后可以生成PDF文件或生成图片文件。
```
- (void)convertToPDF{

    NSData *pdfData = [self.webView convert2PDFData];
    NSString *fileName = [NSString stringWithFormat:@"PDF_%.0f.pdf",[[NSDate date] timeIntervalSince1970]];
    NSString *pdfPath = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]stringByAppendingPathComponent:fileName];
    BOOL result = [pdfData writeToFile:pdfPath atomically:YES];
    if (result) {
        NSLog(@"%@",pdfPath);
        NSLog(@"转换PDF成功");
    }else{
        NSLog(@"转换PDF失败");
    }
}

- (void)convertToImage{

    UIImage *image = [self.webView convert2Image];
    NSData *imageData = UIImagePNGRepresentation(image);
    //    NSString *imagePath = [WYPDFConverter saveDirectory:[NSString stringWithFormat:@"%@_IMG.png",self.fileName]];
    NSString *fileName = [NSString stringWithFormat:@"IMG_%.0f.jpg",[[NSDate date] timeIntervalSince1970]];
    NSString *imagePath = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]stringByAppendingPathComponent:fileName];
    BOOL result = [imageData writeToFile:imagePath atomically:YES];

    if (result) {
        NSLog(@"%@",imagePath);
        NSLog(@"转换Image成功");
    }else{
        NSLog(@"转换Image失败");
    }
}
```

UIWebView+PDFFile.h
```
#import <UIKit/UIKit.h>

@interface UIWebView (PDFFile)


/**
webView转换为PDF数据
*/
- (NSData *)convert2PDFData;


/**
转换成image图片
*/
- (UIImage *)convert2Image;

@end
```

UIWebView+PDFFile.m
```
#import "UIWebView+PDFFile.h"

@implementation UIWebView (PDFFile)

- (NSData *)convert2PDFData{

    //    返回视图的打印格式化
    UIViewPrintFormatter *format = [self viewPrintFormatter];
    UIPrintPageRenderer *render = [[UIPrintPageRenderer alloc] init];
    [render addPrintFormatter:format startingAtPageAtIndex:0];

    // 设置PDF文件每页的尺寸
    CGRect pageRect =  CGRectMake(0, 0, 600, 768);
    //    呈现每个页面的上下文的尺寸大小
    CGRect printableRect = CGRectInset(pageRect, 50, 50);

    [render setValue:[NSValue valueWithCGRect:pageRect] forKey:@"paperRect"];
    [render setValue:[NSValue valueWithCGRect:printableRect] forKey:@"printableRect"];

    NSMutableData *pdfData = [NSMutableData data];
    //    文档信息 可设置为nil
    //    CFMutableDictionaryRef myDictionary = CFDictionaryCreateMutable(nil, 0,
    //                                             &kCFTypeDictionaryKeyCallBacks,
    //                                             &kCFTypeDictionaryValueCallBacks);

    //    CFDictionarySetValue(myDictionary, kCGPDFContextTitle, CFSTR("My PDF File"));
    //    CFDictionarySetValue(myDictionary, kCGPDFContextCreator, CFSTR("My Name"));

    UIGraphicsBeginPDFContextToData(pdfData, pageRect, NULL);

    for (NSInteger i = 0; i < [render numberOfPages]; i++) {

    UIGraphicsBeginPDFPage();
    CGRect bounds = UIGraphicsGetPDFContextBounds();
    [render drawPageAtIndex:i inRect:bounds];
    }

    UIGraphicsEndPDFContext();

    return pdfData;

}

- (UIImage *)convert2Image{

    /*
    将 UIWebView 分屏截取，然后将截取的图片拼接成一张图片
    将 UIWebView 从头，contentOffset = (0, 0)，开始截取webView.bounds.size.height高度的图片，
    */
    CGSize boundsSize = self.bounds.size;
    CGFloat boundsWidth = self.bounds.size.width;
    CGFloat boundsHeight = self.bounds.size.height;
    CGPoint offset = self.scrollView.contentOffset;

    [self.scrollView setContentOffset:CGPointMake(0, 0)];

    CGFloat contentHeight = self.scrollView.contentSize.height;
    NSMutableArray *images = [NSMutableArray array];

    while (contentHeight > 0) {
    UIGraphicsBeginImageContext(boundsSize);

    [self.layer renderInContext:UIGraphicsGetCurrentContext()];

    UIImage *image = UIGraphicsGetImageFromCurrentImageContext();

    UIGraphicsEndImageContext();

    [images addObject:image];

    CGFloat offsetY = self.scrollView.contentOffset.y;

    [self.scrollView setContentOffset:CGPointMake(0, offsetY + boundsHeight)];

    contentHeight -= boundsHeight;
    }

    [self.scrollView setContentOffset:offset];

    UIGraphicsBeginImageContext(self.scrollView.contentSize);

    [images enumerateObjectsUsingBlock:^(UIImage *image, NSUInteger idx, BOOL *stop) {
    [image drawInRect:CGRectMake(0, boundsHeight * idx, boundsWidth, boundsHeight)];
    }];  

    UIImage *fullImage = UIGraphicsGetImageFromCurrentImageContext();

    UIGraphicsEndImageContext();

    return fullImage;


    //    CGFloat scale = [UIScreen mainScreen].scale;
    //    UIImage* image = nil;
    //    
    //    UIGraphicsBeginImageContextWithOptions(self.scrollView.contentSize, NO, scale);
    //    //保存现在的位置和尺寸
    //    CGPoint savedContentOffset = self.scrollView.contentOffset;
    //    CGRect savedFrame = self.frame;
    //    //设置尺寸和内容一样大
    //    self.scrollView.contentOffset = CGPointZero;
    //    self.frame = CGRectMake(0, 0, self.scrollView.contentSize.width, self.scrollView.contentSize.height);
    //    
    //    [self.layer renderInContext: UIGraphicsGetCurrentContext()];
    //    image = UIGraphicsGetImageFromCurrentImageContext();
    //    
    //    //恢复原来的位置和尺寸
    //    self.scrollView.contentOffset = savedContentOffset;
    //    self.frame = savedFrame;
    //    
    //    UIGraphicsEndImageContext();
    //    
    //    return image;
}


@end
```

### 多张图片生成PDF文件
```
- (void)convertImagesToPDFFile{

    NSString *fileName = [NSString stringWithFormat:@"Images_%.0f.pdf",[[NSDate date] timeIntervalSince1970]];
    NSString *pdfPath = [[NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]stringByAppendingPathComponent:fileName];

    NSMutableArray *selectImages = [NSMutableArray arrayWithCapacity:3];
    // 默认添加九张不同大小的图片
    for (NSInteger i = 0; i < 9; i++) {
    UIImage *image = [UIImage imageNamed:[NSString stringWithFormat:@"image%zd.jpg",i]];
    [selectImages addObject:image];
    }
    BOOL result = [self convertPDFWithImages:selectImages filePath:pdfPath];
    if (result) {
        NSLog(@"%@",pdfPath);
        NSLog(@"多张图片转换PDF成功");
    }else{
        NSLog(@"多张图片转换PDF失败");
    }
}

- (BOOL)convertPDFWithImages:(NSArray<UIImage *>*)images filePath:(NSString *)filePath{

    if (!images || images.count == 0) return NO;

    BOOL result = UIGraphicsBeginPDFContextToFile(filePath, CGRectZero, NULL);

    // pdf每一页的尺寸大小

    CGRect pdfBounds = UIGraphicsGetPDFContextBounds();
    CGFloat pdfWidth = pdfBounds.size.width;
    CGFloat pdfHeight = pdfBounds.size.height;

    NSLog(@"%@",NSStringFromCGRect(pdfBounds));

    [images enumerateObjectsUsingBlock:^(UIImage * _Nonnull image, NSUInteger idx, BOOL * _Nonnull stop) {
        // 绘制PDF
        UIGraphicsBeginPDFPage();

        // 获取每张图片的实际长宽
        CGFloat imageW = image.size.width;
        CGFloat imageH = image.size.height;
        //        CGRect imageBounds = CGRectMake(0, 0, imageW, imageH);
        //        NSLog(@"%@",NSStringFromCGRect(imageBounds));

        // 每张图片居中显示
        // 如果图片宽高都小于PDF宽高
        if (imageW <= pdfWidth && imageH <= pdfHeight) {

            CGFloat originX = (pdfWidth - imageW) * 0.5;
            CGFloat originY = (pdfHeight - imageH) * 0.5;
            [image drawInRect:CGRectMake(originX, originY, imageW, imageH)];

        }
        else{
            CGFloat w,h; // 先声明缩放之后的宽高
            //            图片宽高比大于PDF
            if ((imageW / imageH) > (pdfWidth / pdfHeight)){
            w = pdfWidth - 20;
            h = w * imageH / imageW;

            }else{
            //             图片高宽比大于PDF
            h = pdfHeight - 20;
            w = h * imageW / imageH;
            }
            [image drawInRect:CGRectMake((pdfWidth - w) * 0.5, (pdfHeight - h) * 0.5, w, h)];
        }
    }];

    UIGraphicsEndPDFContext();

    return result;
}
```


参考文章：
[iOS PDF文件格式转换](https://www.jianshu.com/p/b6fb4d662969)
[iOS开发之pdf文档的加载与浏览的4种方式](https://www.jianshu.com/p/1d4305a02ea5)
