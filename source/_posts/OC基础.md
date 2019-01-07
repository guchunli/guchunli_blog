---
title: OC基础
date: 2018-03-01 13:54:16
categories: 笔记
tags: [OC,面试]
toc: true
---

# 基础
## 运算符优先级
<!--more-->
<table>
<tr>
<td>优先级</td>
<td>运算符</td>
<td>名称或含义</td>
</tr>

<tr>
<td rowspan = "4">1</td>
<td>[]</td>
<td>数组下标</td>
</tr>
<tr>
<td>()</td>
<td>括号</td>
</tr>
<tr>
<td>.</td>
<td>成员选择（对象）</td>
</tr>
<tr>
<td>-></td>
<td>成员选择（指针）</td>
</tr>

<tr>
<td rowspan = "9">2</td>
<td>-</td>
<td>负号运算符</td>
</tr>
<tr>
<td>(类型)</td>
<td>强制类型转换</td>
</tr>
<tr>
<td>++</td>
<td>自增</td>
</tr>
<tr>
<td>--</td>
<td>自减</td>
</tr>
<tr>
<td>`*`</td>
<td>取值</td>
</tr>
<tr>
<td>&</td>
<td>取地址</td>
</tr>
<tr>
<td>!</td>
<td>逻辑非</td>
</tr>
<tr>
<td>～</td>
<td>按位取反</td>
</tr>
<tr>
<td>sizeof</td>
<td>长度</td>
</tr>

<tr>
<td rowspan="3">3</td>
<td>/</td>
<td>除法</td>
</tr>
<tr>
<td>`*`</td>
<td>乘法</td>
</tr>
<tr>
<td>%</td>
<td>余数</td>
</tr>

<tr>
<td rowspan="2">4</td>
<td>+</td>
<td>加法</td>
</tr>
<tr>
<td>-</td>
<td>减法</td>
</tr>

<tr>
<td rowspan="2">5</td>
<td><<</td>
<td>左移</td>
</tr>
<tr>
<td>>></td>
<td>右移</td>
</tr>

<tr>
<td rowspan="4">6</td>
<td>></td>
<td>大于</td>
</tr>
<tr>
<td>>=</td>
<td>大于等于</td>
</tr>
<tr>
<td><</td>
<td>小于</td>
</tr>
<tr>
<td><=</td>
<td>小于等于</td>
</tr>

<tr>
<td rowspan="2">7</td>
<td>==</td>
<td>等于</td>
</tr>
<tr>
<td>!=</td>
<td>不等于</td>
</tr>

<tr>
<td>8</td>
<td>&</td>
<td>按位与</td>
</tr>

<tr>
<td>9</td>
<td>^</td>
<td>按位异或</td>
</tr>

<tr>
<td>10</td>
<td>|</td>
<td>按位或</td>
</tr>

<tr>
<td>11</td>
<td>&&</td>
<td>逻辑与</td>
</tr>

<tr>
<td>12</td>
<td>||</td>
<td>逻辑或</td>
</tr>

<tr>
<td>13</td>
<td>?:</td>
<td>三目运算符</td>
</tr>

<tr>
<td rowspan="10">14</td>
<td>=</td>
<td>赋值</td>
</tr>
<tr>
<td>／=</td>
<td>除后赋值</td>
</tr>
<tr>
<td>*=</td>
<td>乘后赋值</td>
</tr>
<tr>
<td>%=</td>
<td>取余后赋值</td>
</tr>
<tr>
<td>+=</td>
<td>加后赋值</td>
</tr>
<tr>
<td>-=</td>
<td>减后赋值</td>
</tr>
<tr>
<td><<=</td>
<td>左移后赋值</td>
</tr>
<tr>
<td>>>=</td>
<td>右移后赋值</td>
</tr>
<tr>
<td>&=</td>
<td>按位与后赋值</td>
</tr>
<tr>
<td>^／</td>
<td>按位异或后赋值</td>
</tr>

<tr>
<td>15</td>
<td>,</td>
<td>逗号运算符</td>
</tr>

</table>


## OC
### 动态语言
* 动态编程语言（动态语言）：指程序在运行时可以改变其结构。JS、Python、Ruby属于动态语言，C、C++不属于动态语言。
* 动态类型语言：类型检查在运行时做。
* 静态类型语言：类型检查在运行前判断（如编译期）。方便类型安全，但是与类型安全没有联系。使用继承、接口等势线多态

### OC的三大特性
#### 封装
访问权限修饰符：@public、@protected、@private、@package，默认的修饰符是@private
但是OC是没有修饰符的概念的，如果想让一个方法不被外界访问的话，只需要在.m文件中实现这个方法，不要在头文件中进行定义即可。

#### 继承

#### 多态
`OC是C语言的子类，所以OC是静态语言，但是OC的多态性让其拥有了动态性运行性`
* 动态类型：id类型，运行时决定接收者，动态类型属于弱类型，静态类型属于强类型。
* 动态绑定：运行时判断需要调用什么方法。
* 动态载入：运行时添加代码模块以及其他资源。
对象时运行时类的一个实例。在oc中对象永远是通过指针来引用的。

* 无类型：不做任何检查，甚至不区分指令和数据。
* 弱类型：仅能区分指令和数据。
* 强类型：在编译期进行检查，在没有强制类型转换前，不允许两种不同类型的变量相互操作。

多态：不同对象以自己的方式响应相同的消息的能力叫做多态。子类指针可以赋值给父类对象。
主要是将数据类型的确定由编译时，推迟到了运行时。
(1)对于语句NSString*obj = [[NSData alloc] init]; obj在编译时和运行时分别时什么类型的对象?
答：编译时是NSString的类型;运行时是NSData类型的对象。
(2)id声明的对象具有运行时的特性，即可以指向任意类型的objcetive-c的对象.

[OC中的内存管理机制与属性相关]()

## 多线程（多线程编程指南）
### 进程/线程的区别？同步/异步的区别？并行/并发的区别？
* 进程：正在运行的应用程序，是CPU调度的最小单位，有独立的地址空间，进程中包含的一个或多个执行单元称为线程。
* 线程：是进程中的一条执行路径，共享进程的资源，有自己的堆栈和局部变量，一个线程死掉就等于整个进程死掉。
每条线程可以并行执行不同的任务。
多线程间并发执行，其实是CPU在多条线程间调度（切换）。

* 主线程：
number=1
作用：显示/刷新UI，处理UI事件（点击、拖拽、滚动）
禁止将比较耗时的操作放在主线程，会卡顿

* pthread：跨平台，C，程序员管理生命周期
* NSThread：面向对象，OC，程序员管理生命周期
* GCD：充分利用设备的多核，C，自动管理生命周期
* NSOperation：基于GCD，面向对象，OC，自动管理生命周期

* 执行任务，函数：
同步和异步的区别：能不能开启新线程
同步：只能在当前线程中执行任务，不具备开启新线程的能力。（同步就是必须一件一件事做，等前一件做完了才能做下一件事。）
异步：可以在新线程中执行任务，具备开启新线程的能力。`延迟加载可以避免内存过高，异步加载可以避免线程堵塞。`

* 队列：
串行和并行的区别：任务的执行方式
串行：任务只能一个接一个执行
并行：允许任务同时执行，只在异步函数下才有效，不创建新线程无法并行
全局并发队列：global，系统给每一个应用程序提供了三个concurrent dispatch queues
主队列：主队列中的任务都会在主线程中执行

* 异步函数+并行队列：开启多条（不确定几条）子线程，任务并发执行
* 异步函数+串行队列：开启一条子线程，任务顺序执行
* 同步函数+并发队列：不开启子线程，在主线程执行，任务顺序执行
* 同步函数+串行队列：不开启子线程，在主线程执行，任务顺序执行
* 异步函数+主队列：不开启子线程，在主线程执行，任务顺序执行
* 同步函数+主队列：默认主线程中会死锁，子线程中不会死锁。

* 开启新线程条件：1.异步 2.队列不是主队列，并发队列：开多条，串行：开一条子线程

* 并行和并发的区别：`是否是『同时』`
并行（同时）:两个或多个事件在同一时刻发生。（`你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你支持并行。`）提高效率，资源利用率。
关键是你有`同时`处理多个任务的能力。
并发:两个或者多个事件在同一时间间隔发生。（`你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你支持并发。`）
关键是你有处理多个任务的能力，不一定要同时。

#### NSThread
* 创建线程
```
1.initWithTarget
NSThread* myThread = [[NSThread alloc] initWithTarget:self selector:@selector(doSomething:) object:nil];
//设置线程优先级、线程名称等信息
[myThread start];

2.detachNewThreadSelector
[NSThread detachNewThreadSelector:@selector(doSomething:) toTarget:self withObject:nil];

3.performSelectorInBackground
[self performSelectorInBackground:@selector(doSomething) withObject:nil];
```

* 其他用法：延迟/休眠/死亡
```
//延迟
[self performSelector:@selector(run) withObject:nil afterDelay:2.0];
//休眠
[NSThread sleepForTimeInterval:2.0];
[NSThread sleepUntilDate:(NSDate*)date];
//死亡
[NSThread exit];
```

* 线程间通信
1.通知主线程更新UI界面
```
waitUntilDone参数：
* YES:当前线程要被阻塞，直到主线程将我们制定的代码块执行完，即优先执行updateUI方法
* NO:当前线程不阻塞，会直接向下运行代码，不会立即进入updateUI方法，
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(nullable id)arg waitUntilDone:(BOOL)wait;
```

2.通知其他线程：
```
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(nullable id)arg waitUntilDone:(BOOL)wait;
```

#### GCD
* 延迟执行实现：1.performSelector  2.timer  3.GCD after
```
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 3*NSEC_PER_SEC);
dispatch_after(time, dispatch_get_main_queue(), ^{
NSLog(@"3秒后执行");
});
```

* onece：整个应用程序中只执行一次，不能在懒加载中使用
```
static SingleClass *instance;
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
instance = [[SingleClass alloc]init];
});
return instance;
```

* GCD定时器的特点
    * GCD的定时器不会受到RunLoop运行模式的影响
    * 可以控制任务在主线程还是子线程执行
    * GCD定时器比NSTimer更加准确是因为单位不同，GCD单位是纳秒
    
```
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 3*NSEC_PER_SEC);
dispatch_after(time, dispatch_get_main_queue(), ^{
NSLog(@"3秒后执行");
});
```
    
```
@property (nonatomic,strong)dispatch_source_t timer;    //防止timer被释放

dispatch_source_t timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, dispatch_get_global_queue(0, 0));
dispatch_source_set_timer(timer, DISPATCH_TIME_NOW, 2.0 * NSEC_PER_SEC, 0 * NSEC_PER_SEC);
dispatch_source_set_event_handler(timer, ^{
NSLog(@"run...");
});
dispatch_resume(timer);
self.timer = timer;
```

* dispatch_group_async
可以实现监听一组任务是否完成，完成后得到通知执行其他的操作
```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_t group = dispatch_group_create();
dispatch_group_async(group, queue, ^{
[NSThread sleepForTimeInterval:1];
NSLog(@"group1");
});
dispatch_group_async(group, queue, ^{
[NSThread sleepForTimeInterval:2];
NSLog(@"group2");
});
dispatch_group_async(group, queue, ^{
[NSThread sleepForTimeInterval:3];
NSLog(@"group3");
});
dispatch_group_notify(group, dispatch_get_main_queue(), ^{
NSLog(@"updateUI");
});
```

* dispatch_barrier_async
在前面的任务执行结束后它才执行，而且等它执行完成之后后面的任务才会执行。
```
dispatch_queue_t queue = dispatch_queue_create("barrier", DISPATCH_QUEUE_CONCURRENT);
dispatch_async(queue, ^{
[NSThread sleepForTimeInterval:2];
NSLog(@"dispatch_async1");
});
dispatch_async(queue, ^{
[NSThread sleepForTimeInterval:4];
NSLog(@"dispatch_async2");
});
dispatch_barrier_async(queue, ^{
NSLog(@"dispatch_barrier_async");
[NSThread sleepForTimeInterval:4];

});
dispatch_async(queue, ^{
[NSThread sleepForTimeInterval:1];
NSLog(@"dispatch_async3");
});
```

* dispatch_apply：执行某个代码片段N次。
注意：这个方法没有办法异步执行（为了不阻塞线程可以使用dispatch_async()包装一下再执行）
```
dispatch_apply(5, queue, ^(size_t index) {
// 执行5次
});
```

* dispatch_suspend(myQueue)： 挂起队列
* dispatch_resume(myQueue)：恢复队列
注意：调用dispatch_suspend会增加队列挂起的引用计数，而调用dispatch_resume则会减少引用计数，当引用计数大于0时，队列会保持挂起状态。因此，这队列的挂起和恢复中，我们需要小心使用以避免引用计数计算错误的出现。

#### NSOperation
* NSOperation本身是抽象类，只能只有它的子类，三个子类分别是：NSBlockOperation、NSInvocationOperation以及自定义继承自NSOperation的类

* 队列
1.主队列：通过mainQueue获得，凡是放到主队列中的任务都将在主线程执行
2.非主队列：直接alloc init出来的队列。非主队列同时具备了并发和串行的功能，通过设置最大并发数属性来控制任务是并发执行还是串行执行
```
//1.创建队列
NSOperationQueue *queue = [[NSOperationQueue alloc]init];
//2.设置最大并发数
//注意点：该属性需要在任务添加到队列中之前进行设置
//该属性控制队列是串行执行还是并发执行
//如果最大并发数等于1，那么该队列是串行的，如果大于1那么是并行的
//系统的最大并发数有个默认的值，为-1，如果该属性设置为0，那么不会执行任何任务
queue.maxConcurrentOperationCount = 2;
```

* 线程通信：设置依赖关系
```
//操作A依赖于操作B，线程操作队列在启动线程时就会首先执行B操作，然后执行A。
[operationA addDependency:operationB];
```

### 多线程下，NSMutableArray需不需要开启线程保护
待解决

## 锁
1.NSLock
```
NSLock *theLock = [[NSLock alloc] init];
[theLock lock];
//dosomething
[theLock unlock];
```

2.NSConditionLock 条件锁
```
//公共部分
id condLock = [[NSConditionLock alloc] initWithCondition:NO_DATA];

//线程一，生产者
while(true) {
[condLock lockWhenCondition:NO_DATA];
//生产数据
[condLock unlockWithCondition:HAS_DATA];
}

//线程二，消费者
while (true) {
[condLock lockWhenCondition:HAS_DATA];
//消费
[condLock unlockWithCondition:NO_DATA];
}
```

3.@synchronized(锁对象){}
锁对象必须是全局唯一的
加锁的前提条件：抢夺资源
作用：线程同步，使多线程按顺序执行
注意：加锁的位置，加锁会耗费CPU资源
```
@synchronized(self)
{
// 这段代码对其他 @synchronized(self) 都是互斥的
}
```

4.GCD
```
/*初始化信号量
参数是信号量初始值
*/
_semaphore=dispatch_semaphore_create(1);
/*信号等待
第二个参数：等待时间
*/
dispatch_semaphore_wait(_semaphore, DISPATCH_TIME_FOREVER);
//dosomething
}
//信号通知
dispatch_semaphore_signal(_semaphore);
```

* 其他锁
递归锁：NSRecursiveLock
分布锁：NSDistributedLock，它本身是一个互斥锁，基于文件方式实现锁机制，可以跨进程访问。
互斥锁：如果共享数据已经有其他线程加锁了，线程会进入休眠状态等待锁。一旦被访问的资源被解锁，则等待资源的线程会被唤醒。
自旋锁：如果共享数据已经有其他线程加锁了，线程会以死循环的方式等待锁，一旦被访问的资源被解锁，则等待资源的线程会立即执行。

## runtime与runloop
[iOS runtime与runloop学习笔记](https://guchunli.github.io/2017/03/22/iOS-runtime学习笔记/)

## 数据持久化
### plist文件（属性列表）
### preference（偏好设置）
### 沙盒
### NSKeyedArchiver（归档）
#### 实现NSCoding的自动归档和解档

### SQLite 3
### CoreData

## 网络请求
### http与https
https：HTTP下加入SSL层
区别：
1.https协议需要到ca申请证书。
2.http是超文本传输协议，信息是明文传输，https 则是具有安全性的ssl加密传输协议。
3.http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

### ATS
NSAppTransportSecurity：ATS配置的根节点，配置了节点表示告诉系统要走自定义的ATS设置。
NSAllowsAritraryLoads：是否禁用ATS特性，设置YES就是禁用ATS功能。

### 加密
1.对称加密
1）加密/解密使用相同的密钥
2）加密和解密的过程是可逆的
3）经典算法：AES,DES

2.非对称加密
1）使用公钥加密，使用私钥解密，公钥是公开的，私钥保密
2）加密处理安全，但是性能极差
3）经典算法：RSA

### 原生网络请求类
#### NSURL
作用：对传入的字符串类型采用 ASCII 编码格式
URL最重要的三个部分是 (scheme 方案), (host 主机), (path 路径)
scheme：协议名，如“http”
host：主机域名或 IP 地址。
port ：端口号。
path等

#### 请求报文和响应报文
* 请求首部
1.GET /api/J1/getJ1List HTTP/1.1：请求方式，请求路径，http版本号
2.Host: localhost：3001，主机名，端口号
3.Connection: 连接类型为keep-alive
4.Pragma: no-cache，随报文传送指示的方式
5.Cache-Control : no-cache，随报文传送缓存指示
6.Accept: application/json, text/plain, */*，接受的任意媒体类型, 和响应首部的Content-Type信息对照
7.Origin:  http://localhost:3000，当前访问域名, 与Access-Control-Allow-Origin信息对照
8.User-Agent: Mozilla/5.0，发起请求的应用程序名称
9.Referer: http://localhost:3000/，提供了包含当前请求URI的文档的URL
10.Accept-Encoding: gzip, deflate, br，告诉服务器能够发送哪些编码方式.
11.Accept-Language: zh-CN,zh;q=0.8，告诉服务器能够发送哪些语言.

* 响应首部
1.http版本号，状态码，原因
2.content-Type：如application/json; charset=utf-8，用以区分传输资源
3.Content-Length：主体部分字节长度
4.Date：响应日期.
5.Connection ：连接类型为keep-alive.
6.Access-Control-Allow-Origin ：服务器域名
7.Access-Control-Allow-Methods：服务器实现的方法，GET,HEAD,PUT,POST,DELETE。

#### TCP/IP
HTTP要传送一条报文时, 会以流的形式将报文数据的内容通过一条打开的TCP链接按序传输, TCP收到数据流之后, 会将数据流砍成被称作段的小数据块, 并将段封装在IP分组中.

#### NSURLConnection
* 为什么会废弃NSURLConnection而使用NSURLSession这个网络类呢?
NSURLConnection在iOS9以后已经被苹果弃用了
1.NSURLSession针对下载/上传等复杂的网络操作提供了专门的解决方案，针对普通、上传和下载分别对应三种不同的网络请求任务
2.下载任务方式
NSURLConnection下载文件时，先将整个文件下载到内存，然后再写入沙盒，如果文件比较大，就会出现内存暴涨的情况。
而使用NSURLSessionUploadTask下载文件，会默认下载到沙盒中的tem文件夹中，不会出现内存暴涨的情况，但在下载完成后会将tem中的临时文件删除，需要在初始化任务方法时，在completionHandler回调中增加保存文件的代码。
3.请求方法的控制
NSURLConnection实例化对象，实例化开始，默认请求就发送（同步发送），不需要调用start方法。而cancel 可以停止请求的发送，停止后不能继续访问，需要创建新的请求。
NSURLSession有三个控制方法，取消（cancel），暂停（suspend），继续（resume），暂停后可以通过继续恢复当前的请求任务。
4.断点续传的方式
5.配置信息
NSURLSessionConfiguration类的参数可以设置配置信息，其决定了cookie，安全和高速缓存策略，最大主机连接数，资源管理，网络超时等配置
6.支持app进入后台后的下载等处理。自带多线程，防死锁

#### NSURLSession
1.NSURLSession：会话对象，异步请求, dataTask默认是关闭状态, 需要手动开启dataTask.resume().
2.NSURLSessionConfiguration
* 配置不同的NSURLSession
```
defaultSessionConfiguration //默认配置使用的是持久化的硬盘缓存，存储证书到用户钥匙链。存储cookie到shareCookie。
ephemeralSessionConfiguration //不使用永久持存cookie、证书、缓存的配置，最佳优化数据传输。
backgroundSessionConfigurationWithIdentifier //可以上传下载HTTP和HTTPS的后台任务(程序在后台运行)
```

* 统一设置超时时间、请求头等信息
```
// 构造NSURLSessionConfiguration
NSURLSessionConfiguration *configuration = [NSURLSessionConfiguration defaultSessionConfiguration];
//设置请求超时为10秒钟
configuration.timeoutIntervalForRequest = 10;

//在蜂窝网络情况下是否继续请求（上传或下载）
configuration.allowsCellularAccess = NO;

//配置请求头
configuration.HTTPAdditionalHeaders =@{@"Content-Encoding":@"gzip"};
```

3.NSURLSessionTask
```
NSURLSessionDataTask  //一般的get、post等请求
NSURLSessionUploadTask // 用于上传文件或者数据量比较大的请求
NSURLSessionDownloadTask //用于下载文件或者数据量比较大的请求
NSURLSessionStreamTask //建立一个TCP / IP连接的主机名和端口或一个网络服务对象。
```

函数：
```
- (void)suspend;//暂停
- (void)resume;//开始或者恢复
- (void)cancel;//关闭任务
```

### Bonjour ，WebKit ，BSD Sockets
<!--Bonjour(法语中的你好)-->
Cocoa 网络框架有三层，最底层的是基于 BSD socket库，然后是 Cocoa 中基于 C 的 CFNetwork，最上面一层是 Cocoa 中Foundation 。

## App启动顺序
1.加载main函数
2.UIApplicationMain，创建application对象，创建application的delegate
3.创建主循环，代理对象开始监听
4.didFinishLaunch，创建window
`controller的加载过程`
5.设置window的根控制器
6.如果有storyboard，根据Info.plist获得最主要storyboard的文件名，加载最主要的storyboard
7.显示window
8.didBecomeActive：真正到了这里，才是所有东西全部加载完毕

* controller的加载过程：
1.loadView
2.viewDidLoad
5.viewWillAppear
6.viewWillLayoutSubviews
7.viewDidLayoutSubviews
8.viewDidAppear

* view的加载过程：
9.-(instancetype)initWithCoder:(NSCoder *)aDecoder：如果没有storyboard就会调用initWithFrame，这里两种方法视为一种）
10.- (void)awakeFromNib：在使用IB的时候才会涉及到此方法的使用
11.- (void)viewWillLayoutSubviews：布局子视图
12.- (void)drawRect:(CGRect)rect：使用setNeedsDisplay来定时多次调用本方法

* ApplicationDelegate
1.didFinishLaunch：应用启动完毕调用
2.DidBecomeActive：app程序获取焦点就会调用
3.WillResignActive：APP失去焦点
4.DidEnterBackground：app进入后台的时候调用， 一般在这里保存应用的数据(游戏数据,比如暂停游戏)
5.WillEnterForeground：app程序程序从后台回到前台就会调用
6.DidReceiveMemoryWarning：内存警告，可能要终止程序，清除不需要再使用的内存
7.WillTerminate：程序即将退出调用

1.应用第一次启动：didFinishLaunch->didBecomeActive
2.前台到后台：WillResignActive->DidEnterBackground
3.后台到前台：WillEnterForeground->DidBecomeActive

### 事件传递/事件响应
传递：application->window->controller->view
响应：view->controller->window->application
```
//作用：返回一个view来响应事件
//如果调用了父类的这个方法，那系统就要调用方法pointInside方法，通过这个方法的返回值，来判断当前这个view能不能响应消息。
//如果没有调用父类的方法，那就根据这个方法返回的view，作为响应事件的view。（当然返回nil，就是这个view不响应）
- (nullableUIView *)hitTest:(CGPoint)point withEvent:(nullableUIEvent *)event；

//作用：返回的值可以用来判断是否继续遍历子视图（返回的根据是触摸的point是否在view的frame范围内）
//如果返回的是no，那就不用再去遍历它的子视图，hitTest方法返回的view就是可以响应事件的view。
//如果返回的是YES，那就去遍历它的子视图
- (BOOL)pointInside:(CGPoint)point withEvent:(nullableUIEvent *)event；
```

# 中级
## category分类与extension
1.category与extension的区别
* category可以不用继承系统类，直接给系统类添加方法，最大程度的体现了Objective-C的动态语言特性。可以用来定义私有方法。
* extension为一个类增加私有方法,属性或成员变量,并且新添加的方法一定要予以实现。Extension都是放在.m文件中@implementation的上方。
区别：Extension可以添加属性，category不可以。另外Extension添加的方法是必须要实现的。
2.OC中的私有变量用@private修饰，私有方法用category
3.category为什么不能添加属性
category 它是在运行期决议的。 因为在运行期即编译完成后，对象的内存布局已经确定，如果添加实例变量就会破坏类的内部布局，这对编译型语言来说是灾难性的。

## xib,storyboard,autolayout
缺点：
1.难以维护
2.性能
3.错误定位困难

## UITableView
### 性能优化
1.缓存高度：更新界面之前就把每个 cell 的高度算好，缓存到相对应的 model 中
2.异步绘制
```
//异步绘制
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
CGRect rect = CGRectMake(0, 0, 100, 100);
UIGraphicsBeginImageContextWithOptions(rect.size, YES, 0);
CGContextRef context = UIGraphicsGetCurrentContext();
[[UIColor lightGrayColor] set];
CGContextFillRect(context, rect);

//将绘制的内容以图片的形式返回，并调主线程显示
UIImage *temp = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();

// 回到主线程
dispatch_async(dispatch_get_main_queue(), ^{
//code
});
});
```
3.减少层级：减少SubViews的数量, 在滑动的列表上，多层次的view会导致帧数的下降。
4.hide：尽量少用addView给Cell动态添加View，可以初始化时就添加，然后通过hide来控制是否显示
5.避免离屏渲染
（1）当有图像时，预渲染图像，在bitmap context先将其画一遍，导出成UIImage对象，然后再绘制到屏幕，这会大大提高渲染速度
（2）尽量不要使用透明背景，将cell的opaque值设为Yes，背景色不要使用clearColor，尽量不要使用阴影渐变等
（3）可以在UIView的drawRect方法中自定义绘制
（4）正确地使用UITableViewCell的重用机制
（5）避免阻塞主线程
（6）尽可能重用开销比较大的对象
（7）尽量减少计算的复杂度

#### UITableView的滚动加载进行优化，防止卡顿
1.减少cellForRowAtIndexPath代理中的计算量（cell的内容计算）
首先要提前计算每个cell中需要的一些基本数据，代理调用的时候直接取出；
图片要异步加载，加载完成后再根据cell内部UIImageView的引用设置图片；
图片数量多时，图片的尺寸要跟据需要提前经过transform矩阵变换压缩好（直接设置图片的contentMode让其自行压缩仍然会影响滚动效率），必要的时候要准备好预览图和高清图，需要时再加载高清图。
图片的‘懒加载'方法，即延迟加载，当滚动速度很快时避免频繁请求服务器数据。
尽量手动Drawing视图提升流畅性，而不是直接子类化UITableViewCell，然后覆盖drawRect方法，因为cell中不是只有一个contentview。绘制cell不建议使用UIView，建议使用CALayer。原因要参考UIView和CALayer的区别和联系。

2.减少heightForRowAtIndexPath代理中的计算量（cell的高度计算）
由于每次TableView进行update更新都会对每一个cell调用heightForRowAtIndexPath代理取得最新的height，会大大增加计算时间。如果表格的所有cell高度都是固定的，那么去掉heightForRowAtIndexPath代理，直接设置TableView的rowHeight属性为固定的高度；
如果高度不固定，应尽量将cell的高度数据计算好并储存起来，代理调用的时候直接取，即将height的计算时间复杂度降到O(1)。例如：在异步请求服务器数据时，提前将cell高度计算好并作为dataSource的一个数据存到数据库供随时取用。

### 重用机制
* 可变数组，用来保存当前显示的cell
```
NSMutableArray *visiableCells
```
* 可变字典，用来保存可复用的cell
```
NSMutableDictionary *reusableTableCells
```
可复用的cell使用字典是因为可复用的cell可能不只有一种样式，这里需要字典指定key(也就是reuseIdentifier)来查找是否有可重用样式

#### 重用原理
* 底端出现的cell：reusableTableCells（取出）-> visiableCells（放入）
* 顶端消失的cell：visiableCells（取出）-> reusableTableCells（放入）
*  visiableCells当中如果没有该标识符，alloc即可

在这里先假设iPhone屏幕最多能显示10个cell
1.创建11（最多能显示的次数+1）个单元格
用[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier]创建11次cell，并给cell指定相同的重用标识(当然，能够为不同显示类型的cell指定不同的标识)。而且11个cell所有都增加到visiableCells数组，`此时的reusableTableCells为空`。
2.向下拖动tableView，当cell1全然移出屏幕，而且cell11(它也是alloc出来的，原因同上)全然显示出来的时候。cell11增加到visiableCells，cell1移出visiableCells，增加到reusableTableCells。
3.接下来的拖动当中显示新的cell，cellForRowAtIndexPath会调用，这个时候就执行重用API：传入一个CellIdentifier
接着向下拖动tableView，由于reusableTableCells中已经有值，所以，当须要显示新的cell，cellForRowAtIndexPath再次被调用的时候，tableView dequeueReusableCellWithIdentifier:CellIdentifier，返回cell1。cell1增加到visiableCells，cell1移出reusableTableCells；cell2移出visiableCells，增加到reusableTableCells。之后Cell就能够正常重用了。

* 并不是只有超过屏幕的时候才更新reusableTableCells数组，reloadData方法 和 reloadRowsAtIndex(可能) 对数组进行更新
1. reloadData，这样的情况比较特殊。通常是部分数据发生变化，须要又一次刷新cell显示的内容时调用。在cellForRowAtIndexPath调用中，全部cell都是重用的。我预计reloadData调用后，把visiableCells中全部cell移入reusableTableCells，visiableCells清空。cellForRowAtIndexPath调用后，再把reuse的cell从reusableTableCells取出来，放入到visiableCells。
2. reloadRowsAtIndex，刷新指定的IndexPath。假设调用时reusableTableCells为空，那么cellForRowAtIndexPath调用后，是新创建cell，新的cell增加到visiableCells。老的cell移出visiableCells，增加到reusableTableCells。于是，之后的刷新就有cell做reuse了。


## UICollectionView
### UICollectionViewLayout
* UICollectionView如何显示内容完全由layout(布局对象)决定
UICollectionViewLayout：是一个抽象类，一般情况使用UICollectionViewLayout的子类即可, 只有需要自定义cell显示样式时才需要定义一个类继承于UICollectionViewLayout来自己实现
UICollectionViewFlowLayout(流水布局): 该类是UICollectionViewLayout的子类, 系统已经提供了默认的实现
<!--调用顺序：-->
<!--1.prepareLayout-->
<!--2.-(CGSize) collectionViewContentSize-->
<!--3.-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect-->

# 高级

## UIView 与 CALayer
UIView 的绘制是建立在 CoreGraphic 上的，使用的是 CPU。
CALayer 使用的是 Core Animation，CPU，GPU 通吃，由系统决定使用哪个。
* 绘制
View：自下向上的一层一层的绘制，然后渲染。
Layer处理的是 Texure，利用 GPU 的 Texture Cache 和独立的浮点数计算单元加速 纹理 的处理。

* 从事件的响应来说：
UIView是 CALayer 的代理，layer本身并不能响应事件，因为layer是直接继承自NSObject，不具备处理事件的能力。而 UIView 是继承了UIResponder 的，这也是事件转发的角度上说明，view要比单纯的layer复杂的多。多层次的view再加上各种手势的处理势必导致帧数的下降。

## iOS各版本差异
### iOS9
1.UIStackView
2.ATS

### iOS11

## 性能优化，内存泄漏
内存泄漏（memory leak）： 是指程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存,迟早会被占光。
内存溢出（out of memory）：程序要求的内存，超出了系统所能分配的范围。如：我们用一个int型4字节的数据来装一个float型8字节的数据，就会产生内存溢出。不过我们在编程是可以强制类型转换int XX = (int)float;只取float 4字节数据给int.

[iOS调试与性能优化学习笔记](https://guchunli.github.io/2017/06/05/iOS调试与性能优化学习笔记/)
[iOS最全性能优化](https://www.jianshu.com/p/9c450e512020)
[iOS提高界面流畅度的技巧](https://guchunli.github.io/2017/12/19/iOS提高界面流畅度的技巧/)

## git
[git学习笔记](https://guchunli.github.io/2017/03/03/git学习笔记/)

## 原生与js交互
[OC与JS交互的几种方法](https://guchunli.github.io/2017/07/04/OC与JS交互的几种方法/)

## RAC 函数式，响应式编程
[ReactiveCocoa学习笔记](https://guchunli.github.io/2017/05/20/ReactiveCocoa学习笔记/)


## 动画效果
[iOS动画学习笔记](iOS动画学习笔记)
[RAC与Target-Action、通知、代理、KVO](https://guchunli.github.io/2017/04/23/RAC与Target-Action、通知、代理、KVO/)

## 第三方库
### SDWebImage
* 缓存路径
默认情况下，图片是被存储到`内存`缓存和`磁盘`缓存中的。如果仅仅是想缓存到内存中，可以用方法：storeImage:forKey:toDisk: 第三个参数传NO即可。
* 查找显示图片
sd加载一张图片的时候，会先在内存里面查找是否有这张图片，如果没有会根据图片的md5(url)后的名称去沙盒里面去寻找，是否有这张图片，如果没有会开辟线程去下载，下载完毕后加载到imageview上面，并md(url)为名称缓存到沙盒里面。
* SDWebImage缓存到本地沙盒的哪个路径
`NSCachesDirectory`，SDImageCache 在初始化的时候会注册一些消息通知，在内存警告或退到后台的时 候清理内存图片缓存，应用结束的时候清理过期图片
* SDWebImage加载图片的流程
思路：placeholderImage->内存中查找->硬盘中查找（如有，缓存到内存）->如没有，下载->解码->保存到内存和硬盘
详细流程：
1.入口 setImageWithURL:placeholderImage:options:会先把 placeholderImage显示，然后 SDWebImageManager根据 URL 开始处理图片。
2.进入SDWebImageManager 类中downloadWithURL:delegate:options:userInfo:，交给
SDImageCache从缓存查找图片是否已经下载
queryDiskCacheForKey:delegate:userInfo:.
3.先从内存图片缓存查找是否有图片，如果内存中已经有图片缓存，SDImageCacheDelegate回调 imageCache:didFindImage:forKey:userInfo:到
SDWebImageManager。
4.SDWebImageManagerDelegate 回调
webImageManager:didFinishWithImage: 到 UIImageView+WebCache,等前端展示图片。
5.如果内存缓存中没有，生成 ｀NSOperation ｀
添加到队列，开始从硬盘查找图片是否已经缓存。
6.根据 URL的MD5值Key在硬盘缓存目录下尝试读取图片文件。这一步是在 NSOperation 进行的操作，所以回主线程进行结果回调 notifyDelegate:。
7.如果上一操作从硬盘读取到了图片，将图片添加到内存缓存中（如果空闲内存过小， 会先清空内存缓存）。SDImageCacheDelegate'回调 imageCache:didFindImage:forKey:userInfo:。进而回调展示图片。
8.如果从硬盘缓存目录读取不到图片，说明所有缓存都不存在该图片，需要下载图片， 回调 imageCache:didNotFindImageForKey:userInfo:。
9.共享或重新生成一个下载器 SDWebImageDownloader开始下载图片。
10.图片下载由 NSURLSession 来做，实现相关 delegate
来判断图片下载中、下载完成和下载失败。
11.connection:didReceiveData: 中利用 ImageIO做了按图片下载进度加载效果。
12.connectionDidFinishLoading: 数据下载完成后交给 SDWebImageDecoder做图片解码处理。
13.图片解码处理在一个 NSOperationQueue完成，不会拖慢主线程 UI.如果有需要 对下载的图片进行二次处理，最好也在这里完成，效率会好很多。
14.在主线程 notifyDelegateOnMainThreadWithInfo:
宣告解码完成 imageDecoder:didFinishDecodingImage:userInfo: 回调给 SDWebImageDownloader`。
15.imageDownloader:didFinishWithImage:回调给 SDWebImageManager告知图片 下载完成。
16. 通知所有的 downloadDelegates下载完成，回调给需要的地方展示图片。
17.将图片保存到 SDImageCache中，内存缓存和硬盘缓存同时保存。写文件到硬盘 也在以单独 NSOperation 完成，避免拖慢主线程。
18.SDImageCache 在初始化的时候会注册一些消息通知，在内存警告或退到后台的时 候清理内存图片缓存，应用结束的时候清理过期图片。
19.SDWebImage 也提供了 UIButton+WebCache 和 MKAnnotationView+WebCache，方便使用。
20.SDWebImagePrefetcher 可以预先下载图片。

#### NSCache优于NSDictionary的地方
1.当系统资源将要耗尽时，NSCache可以自动删减缓存
2.NSCache是线程安全的，而NSDictionary则绝对不具备此优势

### cocoapods
#### 私有库
远程私有库可以将你的代码传到第三方托管平台进行公司内部开发人员共享,从而实现组件化开发模式

## 组件化

### MJRefresh
* 基类->基础的下拉/上拉->带有状态文字->(上拉：会回弹到底部/会自动刷新)->能用的子类
MJRefreshComponent->MJRefreshHeader->MJRefreshStateHeader->MJRefreshNormalHeader/MJRefreshGifHeader
                                    ->MJRefreshFooter->MJRefreshBackFooter->MJRefreshBackNormalFooter/MJRefreshBackGifFooter
                                                                   ->MJRefreshAutoFooter->MJRefreshAutoNormalFooter/MJRefreshAutoGifFooter
                                                    
* 创建
block/target-action

* 刷新控件的状态
```
typedef NS_ENUM(NSInteger, MJRefreshState) {
/** 普通闲置状态 */
MJRefreshStateIdle = 1,
/** 松开就可以进行刷新的状态 */
MJRefreshStatePulling,
/** 正在刷新中的状态 */
MJRefreshStateRefreshing,
/** 即将刷新的状态 */
MJRefreshStateWillRefresh,
/** 所有数据加载完毕，没有更多的数据了 */
MJRefreshStateNoMoreData
};
```

* 隐藏
```
// 隐藏时间
header.lastUpdatedTimeLabel.hidden = YES;
// 隐藏状态
header.stateLabel.hidden = YES;
```

### fmmpeg框架
音视频编解码框架，内部使用UDP协议针对流媒体开发，内部开辟了六个端口来接受流媒体数据，完成快速接受之目的。

### 图形和动画 ：Core Animation ，OpenGL ES ，Quartz 2D

## swift
```
//swift中的let是线程安全的
static let instance: NetworkTools = NetworkTools()
class func shareNetworkTools() -> NetworkTools{
return instance
}

private let sharedInstance = Singleton()
class Singleton: NSObject {
class var sharedManager: Singleton {
return sharedInstance;
}
}
```

[Swift学习笔记](https://guchunli.github.io/2017/07/14/Swift学习笔记/)

## react native
[ReactNative基础知识学习笔记](https://guchunli.github.io/2017/07/06/ReactNative基础知识学习笔记/)
[ReactNative使用学习笔记](https://guchunli.github.io/2017/06/21/ReactNative使用学习笔记/)

参考文章：[2017年5月iOS招人心得答案总结](https://www.jianshu.com/p/7d486b24dc21)
[关于NSMutableArray线程安全的思考和实现](http://blog.csdn.net/kongdeqin/article/details/53171189)
[OC知识--彻底理解内存管理(MRC、ARC)](https://www.jianshu.com/p/48665652e4e4)
