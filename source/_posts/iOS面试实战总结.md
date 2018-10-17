---
title: iOS面试实战总结
date: 2017-06-30 14:31:59
categories: 笔记
tags: [面试]
---

# youxin
1.阐述一下 runtime,runloop，并举几个应用场景。
runtime:动态创建类和对象，进行消息发送和转发
runloop运行循环:
一.保持程序的持续运行。
二.处理App中的各类事件（触摸事件、定时器事件、Selector事件）
三.节省CPU资源，提高程序性能：没有事件时就进行睡眠状态
内部实现：do-while循环。

2.请列举你知道的设计模式。
代理，观察者，单例，mvc／mvvm，工厂，策略模式

3.请设计一个账号系统，需考虑持久化，登录态等。


4.请用代码或者伪代码打印输出一个菱形。
```
void print(int n)
{
    int i,j;
    for(i=1; i<=n; i++)
    {
        for(j=1; j<=n-i; j++){ printf(" "); }
        for(j=n-i+1; j<n+i; j++){ printf("*"); }
        printf("\n");
    }

    for(i=n-1; i>=1; i--)
    {
        for(j=1; j<=(n-i); j++){ printf(" "); }
        for(j=n-i+1; j<n+i; j++){ printf("*"); }
        printf("\n");
    }
}
```

5.sdwebimage使用到了哪些缓存机制，缓存到什么路径下。
Memory 和 Disk 双重 Cache 机制。缓存到tmp的cache文件夹下，NSCachesDirectory。

6.是否使用或了解过swift，react native，优缺点是什么。
swift优点：
（1）简洁的语法；
（2）更强的类型安全；
（3）函数式编程的支持；
（4）编写 OS X 下的自动化脚本
swift缺点：
（1）App体积变大
（2）Xcode 支持不够好
（3）第三方库的支持不够多
（4）语言版本更新带来的编译问题

react native优点：
1、原生应用的用户体验
2、跨平台特性
3、热更新（升级）
4、上手快，入门容易，开发效率较高
5、社区繁荣

1.跨平台开发。同一段 Javascript 代码可以被用于 iOS 和 Android 两个平台。相比于以前 iOS 和 Android App 各维护一套逻辑大同小异的代码，React Native 的开发、测试和维护成本要低很多。
2.快速编译。比起 Xcode 中漫长的编译，React Native 采用了热加载（Hot Reload）的即时编译机制，使得 App UI 的开发体验大幅改善，几乎到了和网页开发一样随改随变的效果。
3.快速发布。通过 JSBundle，React Native 可以即时更新 App。相比原来冗长的审核和上传过程，发布和测试新功能的效率大幅提高。
4.渲染和布局更加高效。React Native 可以直接套用网页开发的 CSS 和 flex 机制，摆脱了 autolayout 和 frame 布局中繁琐的数学计算，更加直接简便。
5.简单易学。相比于 iOS 和 Android 的一整套复杂的知识体系，React Native 从本质上来讲就是状态机，对于开发者来讲理解不难，且实际操作可谓入门容易、上手轻松。如果是前端开发者，那么对于 Javascript 本来就有相应了解，用 React Native 开发手机应用更是水到渠成。

react native缺点：
1.对开发人员要求较高
2.发展还不成熟，目前很多ui组件只有ios的实现，android的需要自己实现
3.文档/组件欠缺, 版本升级快

1.第三方依赖。React Native 严重依赖于 Facebook 的维护。苹果在 iOS 上每次技术的更新、政策的改变都会让原来使用了 React Native 代码库受到影响，等待 Facebook 和社区的修复会妨碍 App 的更新和用户体验。前段时间，百度和开发者们弃用
React Native 而迫使的 Facebook 修改开发者权限（License）事件，证明了开发依赖于第三方的风险确实存在。
2.逻辑上的额外开销。直到今天， React Native 依然只是0.49版本，仅仅支持简单的 UI 制作，其不成熟的 API 连复杂的动画都难以实现，更别提 iOS 的底层优化和兼容操作。同时因为操作系统和设备的不同，React Native 得分别进行针对性处理，这对代码库的维护又是一个挑战。
3.联调的困难。对于原生的 iOS 和 Android App 引入 React Native，会增加整个代码库的复杂度，在深入底层原生代码进行 debug 时也是困难重重，可以说是在开发和维护上的成本都有所增加。

7.线上crash如何解决。
bug严重性分析：确定紧急版本更新，迭代版本中修复，热修复。
（1）如果是服务端返回的异常数据没做兼容，就由服务端确保格式正确，客户端看情况是否要做兼容；
（2）如果是升级版本后的由于本地数据库或本地存储的数据格式未兼容等问题，一般需要撤下版本重新提交；
（3）已经发布的版本集成过热修复模块，否则也要重新发布版本。

8.请写出你知道的排序算法及其复杂度。
（1）冒泡排序
（2）插入排序
（3）选择排序
（4）快速排序
![排序算法比较](assets/demo/sort.png)
![排序算法图解](assets/demo/sort_desc.png)

9.AutoreleasePool是如何实现的。
* AutoreleasePool（自动释放池）：是OC中的一种内存自动回收机制，它可以延迟加入AutoreleasePool中的变量release的时机。
* AutoreleasePool与线程：每一个线程都会维护自己的 autoreleasepool 堆栈，每一个 autoreleasepool 只对应一个线程。Autorelease对象是在当前的runloop迭代结束时释放的。
* autorelease何时释放：在没有使用@autoreleasepool的情况,autorelease对象是在当前的runloop迭代结束时释放。每个runloop中都会创建一个 autoreleasepool 并在runloop迭代结束进行释放。如果是手动创建autoreleasepool,自己创建Pool并释放
* AutoreleasePool是如何实现的：通过声明一个__AtAutoreleasePool类型的局部变量__autoreleasepool实现了@autoreleasepool{}。一个线程的autoreleasepool就是一个指针栈，每个runloop迭代中都加入了自动释放池Push和Pop。

10.ipa体积如何压缩。
（1）配置编译选项 （Levels选项内）Genetate Debug Symbols  设置为NO，这个配置选项应该会让你减去小半的体积。
（2）舍弃架构armv7，因为armv7用于支持4s和3gs
（3）编译的版本必须是发布版本
（4）资源：
1）图片：去掉无用的图片；用代码绘制简单的纯色图片；一些对图片要求高的用位图，其他的尽量用矢量图；如果不需要使用透明，可以用jpeg代替PNG
2）音视频：不增加编解码的前提下正确选择格式
3）文件：检查bundle中的无用文件，不要打包到app或者静态库中。可以点击文件，在右侧的file inspector里面的target membership中取消勾选；或者在build phase里面的Copy Bundle Resources中去掉。
4）代码：确定 dead code（代码被定义但从未被调用）被剥离，build setting 里 DEAD_CODE_STRIPPING = YES。 去掉冗余的代码，即使一点冗余代码，编译后体积也是很可观的。
5）第三方库：查找内部使用到的第三方库，一方面可以进行删减代码，用不到的类，直接删除，还有第三方库中的图片资源统统删除掉，如果能够自己手写实现的，那费功夫自己写吧
注意：把product 里面的文件 使用“显示包内容”方式，按照文件大小排序，查看打完包后具体是哪些图片占用太多的空间，并针对性地优化

## http网络请求过程

## 对称加密与非对称加密
### 对称加密
对称加密：加密（encryption）与解密（decryption）使用的是同样的密钥（secret key）。加密和解密算法是公开的，秘钥必须严格保存，如果秘钥泄露，别人就能够用密文+秘钥还原成你的明文。
优点：算法公开、计算量小、加密速度快、加密效率高。
缺点：秘钥的管理和分发非常困难，不够安全。
比如：常见的DES/AES都是属于对称加密算法。

### 非对称加密
非对称加密：需要两个密钥来进行加密和解密，这两个秘钥是公开密钥（简称公钥）和私有密钥（简称私钥），即常说的“公钥加密，私钥加密”或“私钥加密，公钥加密”。私钥只能由一方安全保管，不能外泄，而公钥则可以发给任何请求它的人。非对称加密使用这对密钥中的一个进行加密，而解密则需要另一个密钥。比如，你向银行请求公钥，银行将公钥发给你，你使用公钥对消息加密，那么只有私钥的持有人--银行才能对你的消息解密。与对称加密不同的是，银行不需要将私钥通过网络发送出去，因此安全性大大提高。
优点：安全性更高，公钥是公开的，秘钥是自己保存的，不需要将私钥给别人。
缺点：加密和解密花费时间长、速度慢，只适合对少量数据进行加密。
比如：RSA就是最常用的非对称加密算法。

# momo
## 1.NSString *name，非ARC下重写setter,getter方法
```
-(void)setName:(NSString *)name{
    if (_name != name) {
        [_name release];
        _name = [name copy];
    }
}
- (NSString *)name{
    return [[_name retain]autorelease];
}
```

## 2.简述OC的内存管理模式，包括alloc,retain,copy,release,autorelease,dealloc这些方法的理解。

## 3.描述autorelease对象释放时机，简述autorelasepool的工作机制


## 4.在OC中如何判定两个对象完全相等
[obj1 isEqual:obj2]

## 5.以下作为button的响应方法会有什么问题，能看到log吗
- (void)clickButton{
    dispatch_sync(dispatch_get_main_queue(), ^{
    NSLog("log");
    });
}
线程阻塞，不能，主线程不能使用同步方法

## 6.在一个类中retain一个NSTimer类型的成员变量会有问题吗，为什么
有问题，会retain他的target，target指定为self，会循环引用

## 7.简述C的函数调用和OC的消息传递的机制区别
C函数调用顺序执行
OC消息传递，异步执行

## 8.OC中category的实现原理和runtime有关吗，什么联系
有关，再对下的编译之后，运行期动态添加方法。

## 9.NSOperationQueue和GCD有什么区别，实现机制有什么不同
GCD更简洁，底层C语言实现。

## 10.列举在iOS开发中擅长的模块和其特点使用时的注意问题（例如：音频录制和播放，mapkit定位等）
mapkit：always/wheninuse background

## 11.简述1.2个自己经常使用的第三方库和其优缺点


## atomic／lock
一般情况下并不要求属性必须是“原子的”，因为这并不能保证“线程安全” ( thread safety)，若要实现“线程安全”的操作，还需采用更为深层的锁定机制才行。例如，一个线程在连续多次读取某属性值的过程中有别的线程在同时改写该值，那么即便将属性声明为 atomic，也还是会读到不同的属性值。

## 使用一种数据结构替代NSNotificationCenter
dictionary

## NSSet使用场景

# zhihu
## 1.rebase

## 2.私有库

## 4.NSURL,NSURLSession,NSURLConnection,NSURL


# xiaoyezi

## wkwebview交互过程造成内存泄漏

## Safari 怎么调试加载的h5，怎么控制手机页面调试
* 1.Safari设置-safari偏好设置-高级菜单-在菜单中显示“开发”菜单，选中复选框，这样就能看到开发菜单了
* 2.iPhone设置-safari-高级-打开“javascript”开关和web检查器开关
* 3.iPhone接上Mac，运行手机app里的web页面，在开发菜单中选择连接的手机，找到调试的网页，就能在Safari里面调试了，可以打断点，查看cookie，打印cookie或元素，添加alert

## IM消息状态提醒，比如正在输入之类的

## n个台阶，一次走一步或者两步，有多少种走法

## git回退

## view从创建到展示出来都会经历哪些过程
loadView创建：
(1)[super loadView]
    1)指定了与控制器相关联的xib文件，通过xib文件创建控制器的view
    2)没有明显地传xib文件名，加载与控制器同名的xib文件
    3)没有找到相关联的xib文件，创建一个空白的UIView，赋值给控制器的view
(2)在loadView方法中，通过代码创建view，不需要调用[super loadView]
过程：loadView->viewDidLoad->viewWillAppear->viewWillLayoutSubviews->viewDidLayoutSubviews->viewDidAppear->viewWillDisappear->viewDidDisappear->viewDidUnLoad

# dongao
1.strong，retain，copy，assign，weak，unsafe_unretained
2.远程推送
3.copy，nsmutablearray
4.layoutsubviews什么时候调用
5.内存空间
6.内存优化
7.创建一个对象过程
8.define，const区别
9.五张图片合成一张，异步下载
10.OC动态语言理解

# 高德
* reatin copy 属性 代码

* 通知 主/子线程

* setValue/setObject   forKey/forKeyPath 字典 

* CGRect id NSValue

* ABC排序

* nil、Nil、NULL、NSNull  
nil：指向一个对象的空指针
Nil：指向一个类的空指针
NULL：指向其他类型（如：基本数据类型，C类型）的空指针
NSNull：通常标识集合中的空值
```
NSURL *url = nil;
Class cls = Nil;
int *pointerInt = NULL;
NSArray *arr = [NSArray arrayWithObject:[[NSObject alloc]init],[NSNull null],[[NSObject alloc]init],[[NSObject alloc]init]];
```

* uibutton hitTest

* GCD

* TableView优化

* block 3种

* tableView 实现滚动完成加载数据

* msgSend 消息发送

* runloop timer

* OC 高效

# 

# other
* 热修复
JSPatch ReactNative Lua+Wax

* 列举自己的优点和缺点



参考文章：[iOS之Safari调试webView/H5页面](https://www.cnblogs.com/dianming/p/6902442.html)
