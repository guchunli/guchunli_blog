---
title: iOS多媒体学习笔记
date: 2017-05-06 11:41:05
categories: 学习
tags: [iOS,音频,视频,播放,录制]
toc: true
---

## 音频
### 音效 AudioToolbox.framework
限制：
* 音频播放时间不能超过30s
* 数据必须是PCM或者IMA4格式
* 音频文件必须打包成.caf、.aif、.wav中的一种（注意这是官方文档的说法，实际测试发现一些.mp3也可以播放）
<!--more-->

### 音乐 AVAudioPlayer
AVFoundation.framework中的AVAudioPlayer
AVAudioPlayer可以看成一个播放器，它支持多种音频格式，而且能够进行进度、音量、播放速度等控制。
AVAudioPlayer使用：
* 初始化AVAudioPlayer对象，此时通常指定本地文件路径。
* 设置播放器属性，例如重复次数、音量大小等。
* 调用play方法播放。

### MPMusicPlayerController

### 音频会话 AVAudioSession
支持后台播放：
* 1.设置后台运行模式：在plist文件中添加Required background modes，并且设置item 0=App plays audio or streams audio/video using AirPlay（其实可以直接通过Xcode在Project Targets-Capabilities-Background Modes中设置）
* 2.设置AVAudioSession的类型为AVAudioSessionCategoryPlayback并且调用setActive::方法启动会话。
```
AVAudioSession *audioSession=[AVAudioSession sharedInstance];
[audioSession setCategory:AVAudioSessionCategoryPlayback error:nil];
[audioSession setActive:YES error:nil];
```

* 3.为了能够让应用退到后台之后支持耳机控制，建议添加远程控制事件（这一步不是后台播放必须的）
在iOS中每个应用都有一个音频会话，这个会话就通过AVAudioSession来表示。AVAudioSession同样存在于AVFoundation框架中，它是单例模式设计，通过sharedInstance进行访问。在使用Apple设备时大家会发现有些应用只要打开其他音频播放就会终止，而有些应用却可以和其他应用同时播放，在多种音频环境中如何去控制播放的方式就是通过音频会话来完成的。

### 音频队列服务（Audio Queue Services）
* AVAudioPlayer只能播放本地文件，并且是一次性加载所以音频数据，初始化AVAudioPlayer时指定的URL也只能是File URL而不能是HTTP URL。
* AudioToolbox框架中的音频队列服务Audio Queue Services，支持网络流媒体播放。

### FSAudioStream

## 录音
### AVAudioRecorder
AVFoundation框架中的AVAudioRecorder类
info.plist 添加 `NSMicrophoneUsageDescription`
### 音频队列服务（Audio Queue Services）

## 视频播放
### MPMoviePlayerController
MediaPlayer.framework种的MPMoviePlayerController类，它支持本地视频和网络视频播放，如果要在UI中展示视频需要将view属性添加到界面中。

### MPMoviePlayerViewController
默认全屏播放，开发者在开发的时候直接使用这个视图控制器。
注意：由于MPMoviePlayerViewController的初始化方法做了大量工作（例如设置URL、自动播放、添加点击Done完成的监控等），所以当再次点击播放弹出新的模态窗口的时如果不销毁之前的MPMoviePlayerViewController，那么新的对象就无法完成初始化，这样也就不能再次进行播放。

### AVPlayer
AVPlayer存在于AVFoundation中，它更加接近于底层，所以灵活性也更强。
AVPlayer本身并不能显示视频，而且它也不像MPMoviePlayerController有一个view属性。如果AVPlayer要显示必须创建一个播放器层AVPlayerLayer用于展示，播放器层继承于CALayer，有了AVPlayerLayer之添加到控制器视图的layer中即可。
几个相关类：
* AVAsset：主要用于获取多媒体信息，是一个抽象类，不能直接使用。
* AVURLAsset：AVAsset的子类，可以根据一个URL路径创建一个包含媒体信息的AVURLAsset对象。
* AVPlayerItem：一个媒体资源管理对象，管理者视频的一些基本信息和状态，一个AVPlayerItem对应着一个视频资源。

## 视频截图
### MPMoviePlayerController
### AVAssetImageGenerator
AVFundation框架中的AVAssetImageGenerator
```
/**
*  截取指定时间的视频缩略图
*
*  @param timeBySecond 时间点
*/
-(void)thumbnailImageRequest:(CGFloat )timeBySecond{
    //创建URL
    NSURL *url=[self getNetworkUrl];
    //根据url创建AVURLAsset
    AVURLAsset *urlAsset=[AVURLAsset assetWithURL:url];
    //根据AVURLAsset创建AVAssetImageGenerator
    AVAssetImageGenerator *imageGenerator=[AVAssetImageGenerator assetImageGeneratorWithAsset:urlAsset];
    /*截图
    * requestTime:缩略图创建时间
    * actualTime:缩略图实际生成的时间
    */
    NSError *error=nil;
    CMTime time=CMTimeMakeWithSeconds(timeBySecond, 10);//CMTime是表示电影时间信息的结构体，第一个参数表示是视频第几秒，第二个参数表示每秒帧数.(如果要活的某一秒的第几帧可以使用CMTimeMake方法)
    CMTime actualTime;
    CGImageRef cgImage= [imageGenerator copyCGImageAtTime:time actualTime:&actualTime error:&error];
    if(error){
    NSLog(@"截取视频缩略图时发生错误，错误信息：%@",error.localizedDescription);
    return;
    }
    CMTimeShow(actualTime);
    UIImage *image=[UIImage imageWithCGImage:cgImage];//转化为UIImage
    //保存到相册
    UIImageWriteToSavedPhotosAlbum(image,nil, nil, nil);
    CGImageRelease(cgImage);
}
```
### 第三方
iOS上常用的视频编码、解码框架有：VLC、ffmpeg

## 摄像头（拍照和视频录制）
### UIImagePickerController
UIImagePickerController继承于UINavigationController

### AVFoundation（AVCaptureSession/AVCaptureDevice）
灵活性强。

![优缺点比较](http://o8cfktdb3.bkt.clouddn.com/media.png)

参考原文：[iOS开发系列--音频播放、录音、视频播放、拍照、视频录制](http://www.cnblogs.com/kenshincui/p/4186022.html)
