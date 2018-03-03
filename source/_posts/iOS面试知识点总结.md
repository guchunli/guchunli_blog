---
title: iOS面试知识点总结
date: 2017-06-30 14:31:59
categories: 学习
tags: [iOS,面试]
---

 1.category与extension的区别
* category可以不用继承系统类，直接给系统类添加方法，最大程度的体现了Objective-C的动态语言特性。可以用来定义私有方法。
* extension为一个类增加私有方法,属性或成员变量,并且新添加的方法一定要予以实现。Extension都是放在.m文件中@implementation的上方。
区别：Extension可以添加属性，category不可以。另外Extension添加的方法是必须要实现的。

<!--more-->
2.OC中的私有变量用@private修饰，私有方法用category
3.NSInteger是基本数据类型Int或者Long的别名(NSInteger的定义typedef long NSInteger)，它的区别在于，NSInteger会根据系统是32位还是64位来决定是本身是int还是Long。

4.block

5.Objective-C的内存管理
(1)主要有三种方式ARC(自动内存计数)、手动内存计数、内存池
内存管理机制；当使用new,alloc和copy方法创建一个对象时,该对象的保留计数器值为1.当不再使用该对象时,要负责向该对象发送一条release或autorelease消息.这样,该对象将在使用寿命结束时被销毁.
(2)堆与栈
* 栈:是由编译器自动管理，无需我们手工控制；
* 堆:释放工作由程序员控制，容易产生memory leak。
6.属性关键字
(1)atomic/nonatomic
* atomic提供多线程安全。是防止在未写完的时候被另外一个线程读取，造成数据错误
* non-atomic:在自己管理内存的环境中，解析的访问器保留并自动释放返回的值，如果指定了 nonatomic ，那么访问器只是简单地返回这个值
(2)assign/weak
* 用assign声明的变量在栈中可能不会自动赋值为nil，就会造成野指针错误
* 用weak声明的变量在栈中会自动清空，赋值为nil
assigin 可以用非OC对象,而weak必须用于OC对象
在ARC中,在有可能出现循环引用的时候,往往要通过让其中一端使用weak来解决,比如:delegate代理属性
(3)浅复制和深复制的区别?
* 浅层复制：只复制指向对象的指针，而不复制引用对象本身。
* 深层复制：复制引用对象本身。
(4)copy/mutableCopy
* copy是指针复制（浅拷贝）
* mutableCopy就是对象复制（深拷贝）

7.多态：不同对象以自己的方式响应相同的消息的能力叫做多态。子类指针可以赋值给父类对象。
主要是将数据类型的确定由编译时，推迟到了运行时。
(1)对于语句NSString*obj = [[NSData alloc] init]; obj在编译时和运行时分别时什么类型的对象?
答：编译时是NSString的类型;运行时是NSData类型的对象。
(2)id声明的对象具有运行时的特性，即可以指向任意类型的objcetive-c的对象.
8.NSObject方法
类方法：alloc,class,description
实例方法：init,dealloc,performSelector

9.通知与协议
* 协议有控制链(has-a)的关系，通知没有。
* 通知：一对多。代理/block：一对一。
* OC中的多继承用委托代理实现

10.事件的传递与响应
传递：application->window->controller->view
响应：view->controller->window->application
重写hitTest:withEvent:方法

11.延迟加载：用到的时候才去初始化。
延迟加载可以避免内存过高，异步加载可以避免线程堵塞。

12. 多线程
(1)Object C中创建线程的方法是什么?如果在主线程中执行代码，方法是什么?如果想延时执行代码、方法又是什么?
答：线程创建有三种方法
* 使用NSThread创建
* 使用GCD的dispatch
* 使用子类化的NSOperation,然后将其加入NSOperationQueue;
(2)在主线程执行代码，方法是performSelectorOnMainThread
如果想延时执行代码可以用performSelector:onThread:withObject:waitUntilDone:
(3)进程与线程
进程有独立的地址空间
线程有自己的堆栈和局部变量
(4)队列：串行、并行、全局、主队列

13.持久化存储机制：存到文件，存到NSUserDeaults(plist文件)，sqlite数据库
NSUserDeaults数据存储在沙盒library下的Preferences文件夹。
沙盒目录：documents、library(chches,preferences),tmp
SDWebImage原理：内存中找->沙盒中找，缓存到内存->网络上获取，缓存到内存，缓存到沙盒

14.网络
(1)发起网络请求的对象：AFHTTPSessionManager、ASIHTTPRequest与NSURLConnection
(2)http与socket
* http:短连接，请求方式为客户端主动发起请求，服务器才能给响应，一次请求完毕后则断开连接，以节省资源。服务器不能主动给客户端响应（除非采取http长连接 技术）
* socket:长连接，客户端和服务器可以保持连接通道，双方 都可以主动发送数据

15.fmmpeg框架
答：音视频编解码框架，内部使用UDP协议针对流媒体开发，内部开辟了六个端口来接受流媒体数据，完成快速接受之目的。 
16.Cocoa Touch中一小部分可用的框架：
* 音频和视频：Core Audio ，OpenAL ，Media Library ，AV Foundation
* 数据管理 ：Core Data ，SQLite
* 图形和动画 ：Core Animation ，OpenGL ES ，Quartz 2D
* 网络：Bonjour ，WebKit ，BSD Sockets
* 用户应用：Address Book ，Core Location ，Map Kit ，Store Kit

17.isMemberOfClass 与 isKindOfClass 
isKindOfClass 不仅用来确定一个对象是否是一个类的成员,也可以用来确定一个对象是否派生自该类的类的成员
,而isMemberOfClass 只能做到第一点

18.UIViewController的生命周期
-[ViewController initWithNibName:bundle:]；
-[ViewController init]；
-[ViewController loadView]；
-[ViewController viewDidLoad]；
-[ViewController viewWillDisappear:]；
-[ViewController viewWillAppear:]；
-[ViewController viewDidAppear:]；
-[ViewController viewDidDisappear:]；

Received simulated memory warning.
-[ViewController viewVillUnload]
-[ViewController viewDidUnload]

(1)创建Controller：
* 通过storyBoard:
> [UIStoryboard storyboardWithName:@"" bundle:nil]instantiateInitialViewController` 
> 此处bundle:nil 等价于 [NSBundle mainBundle]
* 通过XIB
    + 加载与控制器同名的XIB文件:使用`init`或`initWithNibName`加载
    + 加载与控制器不同名的XIB Controller文件,通过`loadNibNamed`加载控制器（不同名需要修改file owner）
```
SecondViewController *secondVc = [[[NSBundle mainBundle]loadNibNamed:@"SecondVC" owner:nil options:nil] lastObject]
```

    + 加载与控制器不同名的XIB View文件,通过`loadNibNamed`加载控制器的view（不同名需要修改file owner）
```
SecondViewController *secondVc = [[SecondViewController alloc]init];
secondVc.view = [[[NSBundle mainBundle]loadNibNamed:@"SecondViewController" owner:nil options:nil] lastObject];
```
在创建控制器的时候直接指定要加载xib作为控制器的view,名称不相干
> 需要修改xib文件的fileOwner进行关联
> 需要将fileOwner里面的view指向xib里面的view
(2)创建View:
* 通过XIB：[[NSBundle alloc]loadNibNamed:@"" owner:self options:nil];

19.OC单例
```
+(instancetype)shareNetworkTools{
    static id instance;
    static dispatch_once_t onceToken;
    //onceToken默认等于0，如果是0就执行block，如果不是就不执行
    NSLog(@"%ld",onceToken);
    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
    });
    return instance;
}
```

swift单例
```
/*
//在swift中，类方法中不允许定义静态变量
static var once_t: dispatch_once_t = 0
static var instance: NetworkTools?
//用于获取单例对象的类方法
class func shareNetworkTools() -> NetworkTools{
    print(once_t)
    dispatch_once(&once_t) { () -> Void in
        instance = NetworkTools()
    }
    return instance!
}
*/
//swift中的let是线程安全的
static let instance: NetworkTools = NetworkTools()
class func shareNetworkTools() -> NetworkTools{
    return instance
}

//init方法在需要的时候才创建
override init(){
super.init()
print("----------")
}
```

## youxin
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
Memory 和 Disk 双重 Cache 机制。

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

## 5.以下作为button的响应方法会有什么问题，能看到log吗
- (void)clickButton{
    dispatch_sync(dispatch_get_main_queue(), ^{
    NSLog("log");
    });
}

## 6.在一个类中retain一个NSTimer类型的成员变量会有问题吗，为什么

## 7.简述C的函数调用和OC的消息传递的机制区别

## 8.OC中category的实现原理和runtime有关吗，什么联系

## 9.NSOperationQueue和GCD有什么区别，实现机制有什么不同

## 10.列举在iOS开发中擅长的模块和其特点使用时的注意问题（例如：音频录制和播放，mapkit定位等）

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


参考文章：[iOS之Safari调试webView/H5页面](https://www.cnblogs.com/dianming/p/6902442.html)
