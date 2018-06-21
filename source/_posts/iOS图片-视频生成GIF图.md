---
title: iOS图片/视频生成GIF图
date: 2018-03-15 15:14:58
categories: 笔记
tags: [gif]
toc:
---

## 1.图片生成GIF图
<!--more-->
```
//1.图片生成GIF图
//    NSMutableArray *imgArray = [NSMutableArray array];
//    for (int i=1; i<7; i++) {
//        UIImage *image = [UIImage imageNamed:[NSString stringWithFormat:@"clock%02d.png",i]];
//        [imgArray addObject:image];
//    }
//    [self creatGifWithImages:imgArray];
````

核心方法：
```
#pragma mark - 图片生成gif，需要依赖MobileCoreServices.framework
-(void)creatGifWithImages:(NSMutableArray *)images{

//图像目标
CGImageDestinationRef destination;

//创建输出路径
NSArray *document = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentStr = [document objectAtIndex:0];
NSFileManager *fileManager = [NSFileManager defaultManager];
NSString *textDirectory = [documentStr stringByAppendingPathComponent:@"gif"];
[fileManager createDirectoryAtPath:textDirectory withIntermediateDirectories:YES attributes:nil error:nil];
NSString *path = [textDirectory stringByAppendingPathComponent:@"example.gif"];
NSLog(@"path:%@",path);
NSLog(@"%@",images);

//创建CFURL对象
CFURLRef url = CFURLCreateWithFileSystemPath (

kCFAllocatorDefault,

(CFStringRef)path,

kCFURLPOSIXPathStyle,

false);

//通过一个url返回图像目标
destination = CGImageDestinationCreateWithURL(url, kUTTypeGIF, images.count, NULL);

//设置gif的信息,播放间隔时间,基本数据,和delay时间
NSDictionary *frameProperties = [NSDictionary

dictionaryWithObject:[NSMutableDictionary dictionaryWithObjectsAndKeys:[NSNumber numberWithFloat:6*0.15], (NSString *)kCGImagePropertyGIFDelayTime, nil]

forKey:(NSString *)kCGImagePropertyGIFDictionary];

//设置gif信息
NSMutableDictionary *dict = [NSMutableDictionary dictionaryWithCapacity:2];
[dict setObject:[NSNumber numberWithBool:YES] forKey:(NSString*)kCGImagePropertyGIFHasGlobalColorMap];
[dict setObject:(NSString *)kCGImagePropertyColorModelRGB forKey:(NSString *)kCGImagePropertyColorModel];
[dict setObject:[NSNumber numberWithInt:8] forKey:(NSString*)kCGImagePropertyDepth];
[dict setObject:[NSNumber numberWithInt:0] forKey:(NSString *)kCGImagePropertyGIFLoopCount];

NSDictionary *gifProperties = [NSDictionary dictionaryWithObject:dict

forKey:(NSString *)kCGImagePropertyGIFDictionary];

//合成gif
for (UIImage* dImg in images)
{
CGImageDestinationAddImage(destination, dImg.CGImage, (__bridge CFDictionaryRef)frameProperties);
}

CGImageDestinationSetProperties(destination, (__bridge CFDictionaryRef)gifProperties);
CGImageDestinationFinalize(destination);
CFRelease(destination);

}
```

## 2.视频生成GIF图
```
//2.视频生成GIF图
//创建mp4输出路径
NSArray *document = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentStr = [document objectAtIndex:0];
NSFileManager *fileManager = [NSFileManager defaultManager];
NSString *textDirectory = [documentStr stringByAppendingPathComponent:@"mp4"];
[fileManager createDirectoryAtPath:textDirectory withIntermediateDirectories:YES attributes:nil error:nil];
NSString *path = [textDirectory stringByAppendingPathComponent:@"example.mp4"];

//创建gif输出路径
NSString *gifDirectory = [documentStr stringByAppendingPathComponent:@"gif"];
[fileManager createDirectoryAtPath:gifDirectory withIntermediateDirectories:YES attributes:nil error:nil];
NSString *gifPath = [textDirectory stringByAppendingPathComponent:@"example.gif"];

//从2秒开始，截取8秒
NSRange range = NSMakeRange(2, 8);

//    NSURL *fileURL = [NSURL URLWithString:@"http://o8cfktdb3.bkt.clouddn.com/4.mp4"];
NSURL *fileURL = [NSURL fileURLWithPath:[[NSBundle mainBundle]pathForResource:@"4.mp4" ofType:nil]];

[self interceptVideoAndVideoUrl:fileURL withOutPath:path outputFileType:AVFileTypeMPEG4 range:range intercept:^(NSError *error, NSURL *outPutURL) {

NSLog(@"1.%@",error);
NSLog(@"2.%@",outPutURL);

//        AVURLAsset *asset = [AVURLAsset assetWithURL:outPutURL];
//        [self AnimatedGIFFromMovieAsset:asset timeIncrement:8.f completion:^(NSData *imageData, NSError *error) {
//
//            NSLog(@"1.%@",error);
////            UIImage *img = [UIImage imageWithData:imageData];
////            NSLog(@"%@",img);
//            dispatch_async(dispatch_get_main_queue(), ^{
//                [self showGifImageWithWebViewWithData:imageData];
////                self.desView.image = [UIImage sd_animatedGIFWithData:imageData];
//            });
//        }];

[self createGIFfromURL:outPutURL loopCount:0 delayTime:0.1 gifImagePath:gifPath complete:^(NSError *error, NSURL *gifURL) {

NSLog(@"1.%@",error);
NSLog(@"2.%@",gifURL.absoluteString);
dispatch_async(dispatch_get_main_queue(), ^{
//                [self showGifImageWithWebViewWithPath:gifPath];
self.desView.image = [UIImage sd_animatedGIFWithData:[NSData dataWithContentsOfURL:gifURL]];
});
}];
}];
```

核心方法：
1.截取视频
2.本地视频生成GIF图
```
#pragma mark - 视频生成GIF图
#pragma mark - 1.截取视频
/**
@param videoUrl 视频的URL
@param outPath 输出路径
@param outputFileType 输出视频格式
@param videoRange 截取视频的范围
@param interceptBlock 视频截取的回调
*/
- (void)interceptVideoAndVideoUrl:(NSURL *)videoUrl withOutPath:(NSString *)outPath outputFileType:(NSString *)outputFileType range:(NSRange)videoRange intercept:(InterceptBlock)interceptBlock {

_interceptBlock =interceptBlock;

//不添加背景音乐
NSURL *audioUrl =nil;
//AVURLAsset此类主要用于获取媒体信息，包括视频、声音等
AVURLAsset* audioAsset = [[AVURLAsset alloc] initWithURL:audioUrl options:nil];
AVURLAsset* videoAsset = [[AVURLAsset alloc] initWithURL:videoUrl options:nil];

//创建AVMutableComposition对象来添加视频音频资源的AVMutableCompositionTrack
AVMutableComposition* mixComposition = [AVMutableComposition composition];

//CMTimeRangeMake(start, duration),start起始时间，duration时长，都是CMTime类型
//CMTimeMake(int64_t value, int32_t timescale)，返回CMTime，value视频的一个总帧数，timescale是指每秒视频播放的帧数，视频播放速率，（value / timescale）才是视频实际的秒数时长，timescale一般情况下不改变，截取视频长度通过改变value的值
//CMTimeMakeWithSeconds(Float64 seconds, int32_t preferredTimeScale)，返回CMTime，seconds截取时长（单位秒），preferredTimeScale每秒帧数

//开始位置startTime
CMTime startTime = CMTimeMakeWithSeconds(videoRange.location, videoAsset.duration.timescale);
//截取长度videoDuration
CMTime videoDuration = CMTimeMakeWithSeconds(videoRange.length, videoAsset.duration.timescale);

CMTimeRange videoTimeRange = CMTimeRangeMake(startTime, videoDuration);

//视频采集compositionVideoTrack
AVMutableCompositionTrack *compositionVideoTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeVideo preferredTrackID:kCMPersistentTrackID_Invalid];

// 避免数组越界 tracksWithMediaType 找不到对应的文件时候返回空数组
//TimeRange截取的范围长度
//ofTrack来源
//atTime插放在视频的时间位置
[compositionVideoTrack insertTimeRange:videoTimeRange ofTrack:([videoAsset tracksWithMediaType:AVMediaTypeVideo].count>0) ? [videoAsset tracksWithMediaType:AVMediaTypeVideo].firstObject : nil atTime:kCMTimeZero error:nil];

//视频声音采集(也可不执行这段代码不采集视频音轨，合并后的视频文件将没有视频原来的声音)
AVMutableCompositionTrack *compositionVoiceTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeAudio preferredTrackID:kCMPersistentTrackID_Invalid];

[compositionVoiceTrack insertTimeRange:videoTimeRange ofTrack:([videoAsset tracksWithMediaType:AVMediaTypeAudio].count>0)?[videoAsset tracksWithMediaType:AVMediaTypeAudio].firstObject:nil atTime:kCMTimeZero error:nil];

//声音长度截取范围==视频长度
CMTimeRange audioTimeRange = CMTimeRangeMake(kCMTimeZero, videoDuration);

//音频采集compositionCommentaryTrack
AVMutableCompositionTrack *compositionAudioTrack = [mixComposition addMutableTrackWithMediaType:AVMediaTypeAudio preferredTrackID:kCMPersistentTrackID_Invalid];

[compositionAudioTrack insertTimeRange:audioTimeRange ofTrack:([audioAsset tracksWithMediaType:AVMediaTypeAudio].count > 0) ? [audioAsset tracksWithMediaType:AVMediaTypeAudio].firstObject : nil atTime:kCMTimeZero error:nil];

//AVAssetExportSession用于合并文件，导出合并后文件，presetName文件的输出类型
AVAssetExportSession *assetExportSession = [[AVAssetExportSession alloc] initWithAsset:mixComposition presetName:AVAssetExportPresetPassthrough];


//混合后的视频输出路径
NSURL *outPutURL = [NSURL fileURLWithPath:outPath];

if ([[NSFileManager defaultManager] fileExistsAtPath:outPath])
{
[[NSFileManager defaultManager] removeItemAtPath:outPath error:nil];
}

//输出视频格式 outputFileType:mov或mp4及其它视频格式
assetExportSession.outputFileType = outputFileType;
assetExportSession.outputURL = outPutURL;
//输出文件是否网络优化
assetExportSession.shouldOptimizeForNetworkUse = YES;
[assetExportSession exportAsynchronouslyWithCompletionHandler:^{

dispatch_async(dispatch_get_main_queue(), ^{

switch (assetExportSession.status) {
case AVAssetExportSessionStatusFailed:

if (_interceptBlock) {

_interceptBlock(assetExportSession.error,outPutURL);
}


break;

case AVAssetExportSessionStatusCancelled:{

NSLog(@"Export Status: Cancell");

break;
}
case AVAssetExportSessionStatusCompleted: {

if (_interceptBlock) {

_interceptBlock(nil,outPutURL);
}

break;
}
case AVAssetExportSessionStatusUnknown: {

NSLog(@"Export Status: Unknown");
}
case AVAssetExportSessionStatusExporting : {

NSLog(@"Export Status: Exporting");
}
case AVAssetExportSessionStatusWaiting: {

NSLog(@"Export Status: Wating");
}
}
});
}];
}

#pragma mark--2.本地视频生成GIF图
/**
@param videoURL 视频的路径URL
@param loopCount 播放次数 0即无限循环
@param time 每帧的时间间隔 默认0.25s
@param imagePath 存放GIF图片的文件路径
@param completeBlock 完成的回调
*/

- (void)createGIFfromURL:(NSURL*)videoURL loopCount:(int)loopCount delayTime:(CGFloat )time gifImagePath:(NSString *)imagePath complete:(CompleteBlock)completeBlock {

_completeBlock =completeBlock;


float delayTime = time?:0.1;

// Create properties dictionaries
NSDictionary *fileProperties = [self filePropertiesWithLoopCount:loopCount];
NSDictionary *frameProperties = [self framePropertiesWithDelayTime:delayTime];

AVURLAsset *asset = [AVURLAsset assetWithURL:videoURL];

float videoWidth = [[[asset tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0] naturalSize].width;
float videoHeight = [[[asset tracksWithMediaType:AVMediaTypeVideo] objectAtIndex:0] naturalSize].height;

GIFSize optimalSize = GIFSizeMedium;
if (videoWidth >= 1200 || videoHeight >= 1200)
optimalSize = GIFSizeVeryLow;
else if (videoWidth >= 800 || videoHeight >= 800)
optimalSize = GIFSizeLow;
else if (videoWidth >= 400 || videoHeight >= 400)
optimalSize = GIFSizeMedium;
else if (videoWidth < 400|| videoHeight < 400)
optimalSize = GIFSizeHigh;

//根据所给的时间增长量以及视频的总时长计算一下GIF中图片的数量
float videoLength = (float)asset.duration.value/asset.duration.timescale;
int framesPerSecond = 4;
int frameCount = videoLength*framesPerSecond;

// How far along the video track we want to move, in seconds.
float increment = (float)videoLength/frameCount;

// Add frames to the buffer
NSMutableArray *timePoints = [NSMutableArray array];
for (int currentFrame = 0; currentFrame<frameCount; ++currentFrame) {
float seconds = (float)increment * currentFrame;
CMTime time = CMTimeMakeWithSeconds(seconds, 600);
[timePoints addObject:[NSValue valueWithCMTime:time]];
}


//completion block
NSURL *gifURL = [self createGIFforTimePoints:timePoints fromURL:videoURL fileProperties:fileProperties frameProperties:frameProperties gifImagePath:imagePath frameCount:frameCount gifSize:_gifSize?:GIFSizeMedium];

if (_completeBlock) {

// Return GIF URL
_completeBlock(_error,gifURL);
}

}

#pragma mark - Base methods
- (NSURL *)createGIFforTimePoints:(NSArray *)timePoints fromURL:(NSURL *)url fileProperties:(NSDictionary *)fileProperties  frameProperties:(NSDictionary *)frameProperties gifImagePath:(NSString *)imagePath frameCount:(int)frameCount gifSize:(GIFSize)gifSize{

NSURL *fileURL = [NSURL fileURLWithPath:imagePath];
if (fileURL == nil)
return nil;

CGImageDestinationRef destination = CGImageDestinationCreateWithURL((__bridge CFURLRef)fileURL, kUTTypeGIF , frameCount, NULL);
CGImageDestinationSetProperties(destination, (CFDictionaryRef)fileProperties);

AVURLAsset *asset = [AVURLAsset URLAssetWithURL:url options:nil];
//为目标视频实例化一个生成器
AVAssetImageGenerator *generator = [AVAssetImageGenerator assetImageGeneratorWithAsset:asset];
generator.appliesPreferredTrackTransform = YES;

//设置生成器属性以及GIF属性
float tolerance = 0.01f;
CMTime tol = CMTimeMakeWithSeconds(tolerance, 600);
generator.requestedTimeToleranceBefore = tol;
generator.requestedTimeToleranceAfter = tol;

NSError *error = nil;
CGImageRef previousImageRefCopy = nil;
for (NSValue *time in timePoints) {
CGImageRef imageRef;

#if TARGET_OS_IPHONE || TARGET_IPHONE_SIMULATOR
imageRef = (float)gifSize/10 != 1 ? createImageWithScale([generator copyCGImageAtTime:[time CMTimeValue] actualTime:nil error:&error], (float)gifSize/10) : [generator copyCGImageAtTime:[time CMTimeValue] actualTime:nil error:&error];
#elif TARGET_OS_MAC
imageRef = [generator copyCGImageAtTime:[time CMTimeValue] actualTime:nil error:&error];
#endif

if (error) {

_error =error;
NSLog(@"Error copying image: %@", error);
return nil;

}
if (imageRef) {
CGImageRelease(previousImageRefCopy);
previousImageRefCopy = CGImageCreateCopy(imageRef);
} else if (previousImageRefCopy) {
imageRef = CGImageCreateCopy(previousImageRefCopy);
} else {

_error =[NSError errorWithDomain:NSStringFromClass([self class]) code:0 userInfo:@{NSLocalizedDescriptionKey:@"Error copying image and no previous frames to duplicate"}];
NSLog(@"Error copying image and no previous frames to duplicate");
return nil;
}
CGImageDestinationAddImage(destination, imageRef, (CFDictionaryRef)frameProperties);
CGImageRelease(imageRef);
}
CGImageRelease(previousImageRefCopy);

// Finalize the GIF
if (!CGImageDestinationFinalize(destination)) {

_error =error;

NSLog(@"Failed to finalize GIF destination: %@", error);
if (destination != nil) {
CFRelease(destination);
}
return nil;
}
CFRelease(destination);

return fileURL;
}

#pragma mark - Helpers
CGImageRef createImageWithScale(CGImageRef imageRef, float scale) {

#if TARGET_OS_IPHONE || TARGET_IPHONE_SIMULATOR
CGSize newSize = CGSizeMake(CGImageGetWidth(imageRef)*scale, CGImageGetHeight(imageRef)*scale);
CGRect newRect = CGRectIntegral(CGRectMake(0, 0, newSize.width, newSize.height));

UIGraphicsBeginImageContextWithOptions(newSize, NO, 0);
CGContextRef context = UIGraphicsGetCurrentContext();
if (!context) {
return nil;
}

// Set the quality level to use when rescaling
CGContextSetInterpolationQuality(context, kCGInterpolationHigh);
CGAffineTransform flipVertical = CGAffineTransformMake(1, 0, 0, -1, 0, newSize.height);

CGContextConcatCTM(context, flipVertical);
// Draw into the context; this scales the image
CGContextDrawImage(context, newRect, imageRef);

//Release old image
CFRelease(imageRef);
// Get the resized image from the context and a UIImage
imageRef = CGBitmapContextCreateImage(context);

UIGraphicsEndImageContext();
#endif

return imageRef;
}

#pragma mark - Properties

- (NSDictionary *)filePropertiesWithLoopCount:(int)loopCount {
return @{(NSString *)kCGImagePropertyGIFDictionary:
@{(NSString *)kCGImagePropertyGIFLoopCount: @(loopCount)}
};
}

- (NSDictionary *)framePropertiesWithDelayTime:(float)delayTime {

return @{(NSString *)kCGImagePropertyGIFDictionary:
@{(NSString *)kCGImagePropertyGIFDelayTime: @(delayTime)},
(NSString *)kCGImagePropertyColorModel:(NSString *)kCGImagePropertyColorModelRGB
};
}
```
