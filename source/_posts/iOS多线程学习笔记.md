---
title: iOS多线程学习笔记
date: 2017-06-30 16:45:27
categories: 学习
tags: [iOS,多线程]
toc: true
---

多线程的三种使用方式：NSThread，NSOperationQueue和NSOperation，GCD。
<!--more-->
## NSThread
### 创建线程的三种方式
（1）创建、启动线程
```
NSThread* myThread = [[NSThread alloc] initWithTarget:self selector:@selector(doSomething:) object:nil];  
//设置线程优先级、线程名称等信息
[myThread start];
```

（2）创建线程后自动启动线程
```
[NSThread detachNewThreadSelector:@selector(doSomething:) toTarget:self withObject:nil];  
```

（3）隐式创建并启动线程
```
[self performSelectorInBackground:@selector(doSomething) withObject:nil];
```
注意：三种创建方式都只能传一个参数，如果需要传递多参数，可以封装一个对象将多个参数保存起来。

### 回到主线程
performSelectorOnMainThread：该方法主要用来在主线程上修改页面UI的状态
waitUntilDone参数：
* YES:当前线程要被阻塞，直到主线程将我们制定的代码块执行完，即优先执行updateUI方法
* NO:当前线程不阻塞，会直接向下运行代码，不会立即进入updateUI方法，
* 当前线程为主线程的时候，waitUntilDone:YES参数无效，那么updateUI方法会马上执行
```
[self performSelectorOnMainThread:@selector(updateUI:) withObject:image waitUntilDone:YES];  
```

获取主线程、多线程：`[NSThread mainThread]`  `[NSThread isMultiThreaded]`
### 线程间通讯
* 通知主线程更新UI界面
```
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(nullable id)arg waitUntilDone:(BOOL)wait;
```

* 通知其他线程：
```
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(nullable id)arg waitUntilDone:(BOOL)wait;
```

### 延迟执行
（1）调用NSObject的方法:
```
[self performSelector:@selector(run) withObject:nil afterDelay:2.0];
```

（2）
```
[NSThread sleepForTimeInterval:2.0];
[NSThread sleepUntilDate:(NSDate*)date]
```

### 死亡：+ (void)exit()

## NSOperationQueue和NSOperation
### 创建
NSOperation是个抽象类，使用它必须用它的子类。NSOperation的两个子类：NSInvocationOperation 和 NSBlockOperation。
（1）NSInvocationOperation 
```
/*创建一个调用操作
object:调用方法参数
*/
NSInvocationOperation *invocationOperation=[[NSInvocationOperation alloc]initWithTarget:self selector:@selector(loadImage) object:nil];
//创建完NSInvocationOperation对象并不会调用，它由一个start方法启动操作，但是注意如果直接调用start方法，则此操作会在主线程中调用，一般不会这么操作,而是添加到NSOperationQueue中
//    [invocationOperation start];

//创建操作队列
NSOperationQueue *operationQueue=[[NSOperationQueue alloc]init];
//注意添加到操作队后，队列会开启一个线程执行此操作
[operationQueue addOperation:invocationOperation];
```

（2）NSBlockOperation：相比NSInvocationOperation 更简单，推荐
```
//方法1：创建操作块添加到队列
//创建多线程操作
NSBlockOperation *blockOperation=[NSBlockOperation blockOperationWithBlock:^{
    [self loadImage:[NSNumber numberWithInt:i]];
}];
//创建操作队列
[operationQueue addOperation:blockOperation];

//方法2：直接使用操队列添加操作
[operationQueue addOperationWithBlock:^{
[self loadImage:[NSNumber numberWithInt:i]];
}];
```

（3）继承NSOperation
在.m文件中实现main方法，main方法编写要执行的代码。

### 并发数、优先级
* 设置队列并发数
默认情况下是-1，-1表示没有限制，这样会同时运行队列中的全部的操作。
```
[queue setMaxConcurrentOperationCount:5];
```

* 设置线程优先级
> 单独设置线程在队列中的优先级不起作用,必须先将队列挂起(还得设置并发誓),等线程都加进去,再开启
```
queue.suspended = YES;
operation.queuePriority = NSOperationQueuePriorityHigh;
[queue addOperation:operation];
[queue setSuspended:NO];
```

### 依赖关系
每个NSOperation可以设置依赖线程。假设操作A依赖于操作B，线程操作队列在启动线程时就会首先执行B操作，然后执行A。
以下代码实现了优先加载最后一张图片：
```
//会优先执行operation2
[operation1 addDependency:operation2];
```

## GCD
### dispatch queue
（1）serial
同时只执行一个任务。通常用于同步访问特定的资源或数据。
（2）concurrent
可以并发地执行多个任务，但是执行完成的顺序是随机的。
（3）main
主队列，它是全局可用的serial queue。在主线程上执行任务。
（4）global
系统给每一个应用程序提供了三个concurrent dispatch queues。这三个并发调度队列是全局的，它们只有优先级的不同。
因为是全局的，我们不需要去创建。我们只需要通过使用函数dispath_get_global_queue去得到队列。

### dispatch_group_async
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

### dispatch_barrier_async
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

### dispatch_apply 
执行某个代码片段N次。
注意：这个方法没有办法异步执行（为了不阻塞线程可以使用dispatch_async()包装一下再执行）
```
dispatch_apply(5, queue, ^(size_t index) {
// 执行5次
});
```

### dispatch_time()： 延迟一定的时间后执行。
```
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 3*NSEC_PER_SEC);
dispatch_after(time, dispatch_get_main_queue(), ^{
    NSLog(@"3秒后执行");
});
```

### 单例
```
static SingleClass *instance;
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
    instance = [[SingleClass alloc]init];
});
return instance;
```

### dispatch_suspend(myQueue)： 挂起队列
### dispatch_resume(myQueue)：恢复队列

## 线程同步
加锁的几种方式：
### NSLock
注意：lock和unlock之间的”加锁代码“应该是抢占资源的读取和修改代码，不要将过多的其他操作代码放到里面，否则一个线程执行的时候另一个线程就一直在等待，就无法发挥多线程的作用了。
```
theLock = [[NSLock alloc] init];  
　　[theLock lock];
　　//dosomething
[theLock unlock];  
```

### NSCondition
```
ticketsCondition = [[NSCondition alloc] init];  
[ticketsCondition lock];  
if (_imageNames.count>0) {
    [_condition wait];
}else{
    [_imageNames addObject:[NSString stringWithFormat:@"http://images.cnblogs.com/cnblogs_com/kenshincui/613474/o_%i.jpg",_currentIndex++]];
    //创建完图片则发出信号唤醒其他等待线程
    [_condition signal];
}
[ticketsCondition unlock];  
// [ticketsCondition signal]：发送信号的方式
// [ticketsCondition wait]：在一个线程唤醒另外一个线程的等待
```

### @synchronized：更简单，推荐
```
@synchronized(anObj)
{
//dosomething
}
```

### GCD
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

### iOS中的其他类型锁
NSRecursiveLock ：递归锁，有时候“加锁代码”中存在递归调用，递归开始前加锁，递归调用开始后会重复执行此方法以至于反复执行加锁代码最终造成死锁，这个时候可以使用递归锁来解决。使用递归锁可以在一个线程中反复获取锁而不造成死锁，这个过程中会记录获取锁和释放锁的次数，只有最后两者平衡锁才被最终释放。
```
NSRecursiveLock *theLock = [[NSRecursiveLock alloc] init];
void MyRecursiveFunction(int value)
{
[theLock lock];
if (value != 0)
<span style="font-size:14px;"> </span>{
–value;
MyRecursiveFunction(value);
}
[theLock unlock];
}

MyRecursiveFunction(5);

```

NSDistributedLock：分布锁，它本身是一个互斥锁，基于文件方式实现锁机制，可以跨进程访问。
pthread_mutex_t：同步锁，基于C语言的同步锁机制，使用方法与其他同步锁机制类似。
互斥锁：如果共享数据已经有其他线程加锁了，线程会进入休眠状态等待锁。一旦被访问的资源被解锁，则等待资源的线程会被唤醒。
自旋锁：如果共享数据已经有其他线程加锁了，线程会以死循环的方式等待锁，一旦被访问的资源被解锁，则等待资源的线程会立即执行。

注意：调用dispatch_suspend会增加队列挂起的引用计数，而调用dispatch_resume则会减少引用计数，当引用计数大于0时，队列会保持挂起状态。因此，这队列的挂起和恢复中，我们需要小心使用以避免引用计数计算错误的出现。

参考链接：[荣芳志专栏中的文章](http://blog.csdn.net/totogo2010/article/details/8010231)
　　　　　[iOS开发系列--并行开发其实很容易](http://www.cnblogs.com/kenshincui/p/3983982.html)

