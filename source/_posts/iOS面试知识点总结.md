---
title: iOS面试知识点总结
date: 2017-06-30 14:31:59
categories: 学习
tags: [iOS,面试]
---

1.category与extension的区别
* category可以不用继承系统类，直接给系统类添加方法，最大程度的体现了Objective-C的动态语言特性。可以用来定义私有方法。
* extension为一个类增加私有方法,属性或成员变量,并且新添加的方法一定要予以实现。Extension都是放在.m文件中@implementation的上方。
区别：Extension可以添加属性。另外Extension添加的方法是必须要实现的。
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

