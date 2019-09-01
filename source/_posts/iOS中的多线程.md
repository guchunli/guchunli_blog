---
title: iOS中的多线程
copyright: true
date: 2019-08-15 10:20:54
categories: 笔记
tags:
toc: true
---

## 基本概念
* 进程与线程
进程：一个正在运行的应用程序在操作系统中被视为一个进程，包含着一个运行程序所需要的资源。进程有独立的地址空间。
<!--more-->
线程：是进程中的基本执行单元，是操作系统分配CPU时间的基本单位。线程有自己的堆栈和局部变量，但线程之间没有单独的地址空间，一个线程死掉就等于整个进程死掉。
一个应用程序就是一个进程，而一个进程可以分为多个线程。

* 多线程
一个进程中可以开启多条线程，每条线程可以并行（同时）执行不同的任务。
多线程必须要有多核的cpu支持，对于多核cpu，多个线程会在多个cpu中同时运行，从而加快程序的执行速度。

* 同步与异步（机制）
同步：顺序执行。
异步：同时执行。一般通过状态（轮询）、通知和回调来通知调用者。对于异步调用，调用的返回并不受调用者控制。
区别：请求发出后，是否需要等待结果，才能继续执行其他操作。
同步一般顺序执行，异步大多数时候是多线程。

* 阻塞与非阻塞
指程序在等待消息通知时的状态。(无所谓同步或者异步)
阻塞调用：是指调用结果返回之前，当前线程会被挂起。
非阻塞调用：指在不能立刻得到结果之前，该调用不会阻塞当前线程。

* 串行与并发
串行：一个人同时做几件事
并行：几个人同时做一件事

* 并发与并行
并行：有`同时`处理多个任务的能力（一边吃饭，一边打电话）
并发：有处理多个任务的能力，不一定要`同时`（吃饭过程中，接了电话，接着吃饭）

* 多线程与异步与并行
异步是当一个调用请求发送给被调用者，而调用者不用等待其结果的返回。
实现异步可以采用多线程技术或则交给另外的进程来处理。

* 同步/异步与阻塞/非阻塞
同步/异步：关注的是“下载完成消息”通知的方式（机制）
阻塞/非阻塞：关注的是在等待“下载完成消息”通知时的状态（能不能干其他任务）

同步非阻塞：专心做一件事，等待过程中什么别的事都不做。
异步阻塞：等待任务完成的消息通知，不是在处理消息时阻塞，而是在等待消息通知时被阻塞。
同步非阻塞：可以去干别的事，但是要时不时回来看看任务是否完成，需要在两种不同行为之间来回切换。
异步非阻塞：去干别的事，收到消息通知再回去处理，没有在两种不同搞得操作中切换。

## 多线程技术
### NSThread
支持多线程和异步操作，使用简单，比performSelector稍微复杂一些，但是没有没有线程的一些控制和调度的操作
* 优点：简单
* 缺点：没有串行并线队列，不能实现高级线程调度,和performSelector是一样的。
```
// 阻塞（暂停）线程
[NSThread sleepForTimeInterval:2];

// 强制停止线程:
[NSThread exit];

// 创建子线程执行
NSThread *thread1 = [[NSThread alloc]initWithTarget:self selector:@selector(test:) object:@"111"];
[thread1 start];
// 类方法
[NSThread detachNewThreadSelector:@selector(test:) toTarget:self withObject:@"222"];

// block方式，iOS系统>=10才可用
NSThread *thread2 = [[NSThread alloc]initWithBlock:^{
[self test:@"333"];
}];
[thread2 start];
[NSThread detachNewThreadWithBlock:^{
<!--[self test:@"444"];-->
}];


- (void)test:(NSString *)params{
NSLog(@"%@",params);
NSLog(@"%ld--%@",[NSThread isMainThread],[NSThread currentThread]);
}
```

### performSelector
实际还是NSThread
```
// 同步（主线程执行）
[self performSelector:@selector(test:) withObject:@"111"];
// 异步，线程阻塞
[self performSelector:@selector(test:) withObject:@"222" afterDelay:3];
// 异步，非阻塞（子线程执行）
[self performSelectorInBackground:@selector(test:) withObject:@"333"];
// 指定线程执行
[self performSelector:@selector(test:) onThread:[NSThread mainThread] withObject:@"444" waitUntilDone:NO];
[self performSelector:@selector(test:) onThread:[[NSThread alloc]init] withObject:@"555" waitUntilDone:NO];
//线程阻塞 最后一个参决定是同步还是异步
// 主线程上执行，主线程阻塞，waitUntilDone:YES：等待执行完成顺序执行（同步），waitUntilDone:NO 先执行后面语句（异步）
[self performSelectorOnMainThread:@selector(test:) withObject:@"666" waitUntilDone:YES];
[self performSelectorOnMainThread:@selector(test:) withObject:@"777" waitUntilDone:NO];
```

### GCD
支持多线程，同步异步操作，线程控制，线程队列，线程信号等等，IOS和OS中最强大的线程管理都是是它了。 要说缺点的话，就是代码比较复杂，前面能实现的就用前面的把，如果实现不了，那找它准没错。
* 优点：有很多串行并线队列多线程，block实现线程方法，高级，好用，方法多。
* 缺点：在很多不需要高级控制线程的场景可以不用使用GCD

* 同步执行（sync）：
同步添加任务到指定的队列中，在添加的任务执行结束之前，会一直等待，直到队列里面的任务完成之后再继续执行。
只能在当前线程中执行任务，不具备开启新线程的能力。

* 异步执行（async）：
异步添加任务到指定的队列中，它不会做任何等待，可以继续执行任务。
可以在新的线程中执行任务，具备开启新线程的能力。

区别：是否等待队列的任务执行结束，以及是否具备开启新线程的能力。
注意：异步执行（async）虽然具有开启新线程的能力，但是并不一定开启新线程。这跟任务所指定的队列类型有关。

* 队列
存放任务的队列。队列是一种特殊的线性表，采用 FIFO（先进先出）的原则。

* 串行队列（Serial Dispatch Queue）：
每次只有一个任务被执行。让任务一个接着一个地执行。
（只开启一个线程，一个任务执行完毕后，再执行下一个任务）

* 并发队列（Concurrent Dispatch Queue）：
可以让多个任务并发（同时）执行。（可以开启多个线程，并且同时执行任务）
并发和串行：任务的执行方式。

* 主队列
特殊的串行队列，所有放到主队列中的任务都会放到主线程中执行。
dispatch_get_main_queue() 方法获得主队列。

* 全局并发队列
系统提供的默认的并发队列。
dispatch_get_global_queue 方法来获取全局并发队列。

* 队列与任务的不同组合方式
同步执行 + 并发队列：没有开启新线程，串行执行任务
异步执行 + 并发队列：有开启新线程，并发执行任务
同步执行 + 串行队列：没有开启新线程，串行执行任务
异步执行 + 串行队列：有开启新线程（1条），串行执行任务
同步执行 + 主队列：死锁卡住不执行
异步执行 + 主队列：没有开启新线程，串行执行任务(虽然 异步执行 具备开启线程的能力，但因为是主队列，所以所有任务都在主线程中)

注意：并发队列 的并发功能只有在异步（dispatch_async）方法下才有效。`同步执行 + 并发队列` 虽然并发队列可以开启多个线程，并且同时执行多个任务。但是因为本身不能创建新线程，只有当前线程这一个线程（同步任务不具备开启新线程的能力）

* 死锁
（1）主队列死锁：`主队列中追加的同步任务` 和 `主线程本身的任务` 两者之间相互等待，阻塞了`主队列`。（注意：阻塞的是当前队列，不是当前线程）
而在 `其他线程` 调用 `主队列`+`同步执行`，则不会阻塞 `主队列`，自然也不会造成死锁问题。最终的结果是：不会开启新线程，串行执行任务。
（2）串行队列死锁：在`异步执行`+`串行队列`的任务中，又嵌套了`当前的串行队列`，然后进行`同步执行`，`串行队列中追加的任务` 和 `串行队列中原有的任务` 两者之间相互等待，阻塞了`串行队列`，最终造成了串行队列所在的线程（子线程）死锁问题。

在主线程中调用`同步执行+主队列`：互相等待卡住死锁
在其他线程中调用`同步执行+主队列`：不会开启新线程，顺序执行。
```
// 1.队列的创建方式
/*
* const char *_Nullable label：队列的唯一标识符，用于 DEBUG，可为空。队列的名称推荐使用应用程序 ID 这种逆序全程域名
* dispatch_queue_attr_t _Nullable attr：识别是串行队列还是并发队列。DISPATCH_QUEUE_SERIAL/DISPATCH_QUEUE_CONCURRENT
*/
dispatch_queue_t queue1 = dispatch_queue_create(DISPATCH_QUEUE_PRIORITY_DEFAULT, DISPATCH_QUEUE_SERIAL);
dispatch_queue_t queue2 = dispatch_get_main_queue();
/*
* long identifier：队列优先级，一般用 DISPATCH_QUEUE_PRIORITY_DEFAULT
* unsigned long flags：0
*/
dispatch_queue_t queue3 = dispatch_queue_create(DISPATCH_QUEUE_PRIORITY_DEFAULT, DISPATCH_QUEUE_CONCURRENT);
dispatch_queue_t queue4 = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

// 2.任务的创建方式
// 同步任务
dispatch_sync(queue4, ^{

});
// 异步任务
dispatch_async(queue4, ^{

});

/*
产生死锁的两个情况:
当同步函数内的队列是主队列时,会产生死锁
使用sync函数往当前串行队列中添加任务,就会产生死锁
*/
// 主队列+同步任务 主线程执行会死锁
[self performSelector:@selector(syncFunction) withObject:nil];
// 主队列+同步任务 子线程执行不会死锁
[self performSelectorInBackground:@selector(syncFunction) withObject:nil];

-（void）syncFunction{
    // 在主线程下执行以下方法
    dispatch_sync(dispatch_get_main_queue(), ^{

    });
}
// 3.串行队列中的死锁问题
dispatch_queue_t queue = dispatch_queue_create("test.queue", DISPATCH_QUEUE_SERIAL);
dispatch_async(queue, ^{    // 异步执行 + 串行队列
    dispatch_sync(queue, ^{  // 同步执行 + 当前串行队列
        // 追加任务 1
        [NSThread sleepForTimeInterval:2];              // 模拟耗时操作
        NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程
    });
});

```

```

//获取一个队列
dispatch_queue_t defaultQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

//dispatch_async：异步方式执行方法（最常用）
//    dispatch_async(defaultQueue, ^{
//        [self function1];
//    });

//dispatch_sync：同步方式使用场景，比较少用，一般与异步方式进行调用
//    dispatch_async(defaultQueue, ^{
//       NSMutableArray *array = [self GCD_sync_Function];
//       dispatch_async(dispatch_get_main_queue(), ^{
//           //利用获取的arry在主线程中更新UI
//
//       });
//    });

//dispatch_once：一次性执行，常常用户单例模式.这种单例模式更安全
//    static dispatch_once_t onceToken;
//    dispatch_once(&onceToken, ^{
//        // code to be executed once
//        NSLog(@"dispatch_once");
//    });

//dispatch_after 延迟异步执行
//    dispatch_time_t popTime = dispatch_time(DISPATCH_TIME_NOW, 2.0 * NSEC_PER_SEC);
//    dispatch_after(popTime, defaultQueue, ^{
//        NSLog(@"dispatch_after");
//    });

//dispatch_group_async 组线程可以实现线程之间的串联和并联操作
//    dispatch_group_t group = dispatch_group_create();
//    NSDate *now = [NSDate date];
//    //做第一件事 2秒
//    dispatch_group_async(group, defaultQueue, ^{
//        [NSThread sleepForTimeInterval:2];
//         NSLog(@"work 1 done");
//    });
//    //做第二件事 5秒
//    dispatch_group_async(group, defaultQueue, ^{
//        [NSThread sleepForTimeInterval:5];
//        NSLog(@"work 2 done");
//    });
//
//    //两件事都完成后会进入方法进行通知
//    dispatch_group_notify(group, defaultQueue, ^{
//        NSLog(@"dispatch_group_notify");
//        NSLog(@"%f",[[NSDate date]timeIntervalSinceDate:now]);//总共用时5秒，因为2个线程同时进行
//    });

//dispatch_barrier_async :作用是在并行队列中，等待前面的队列执行完成后在继续往下执行
//    dispatch_queue_t concurrentQueue = dispatch_queue_create("my.concurrent.queue", DISPATCH_QUEUE_CONCURRENT);
//    dispatch_async(concurrentQueue, ^{
//        [NSThread sleepForTimeInterval:2];
//        NSLog(@"work 1 done");
//    });
//    dispatch_async(concurrentQueue, ^{
//        [NSThread sleepForTimeInterval:2];
//        NSLog(@"work 2 done");
//    });
//    //等待前面的线程完成后执行
//    dispatch_barrier_async(concurrentQueue, ^{
//         NSLog(@"dispatch_barrier_async");
//    });
//
//    dispatch_async(concurrentQueue, ^{
//        [NSThread sleepForTimeInterval:3];
//        NSLog(@"work 3 done");
//    });

//dispatch_semaphore 信号量的使用，串行异步操作
//    dispatch_semaphore_create　　　创建一个semaphore
//　　 dispatch_semaphore_signal　　　发送一个信号
//　　 dispatch_semaphore_wait　　　　等待信号

/*应用场景1：马路有2股道，3辆车通过 ，每辆车通过需要2秒
*条件分解:
马路有2股道 <=>  dispatch_semaphore_create(2) //创建两个信号
三楼车通过 <=> dispatch_async(defaultQueue, ^{ } 执行三次
车通过需要2秒 <=>  [NSThread sleepForTimeInterval:2];//线程暂停两秒
*/

dispatch_semaphore_t semaphore = dispatch_semaphore_create(2);

dispatch_async(defaultQueue, ^{
dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
[NSThread sleepForTimeInterval:2];
NSLog(@"carA pass the road");
dispatch_semaphore_signal(semaphore);
});
dispatch_async(defaultQueue, ^{
dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
[NSThread sleepForTimeInterval:2];
NSLog(@"carB pass the road");
dispatch_semaphore_signal(semaphore);
});
dispatch_async(defaultQueue, ^{
dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
[NSThread sleepForTimeInterval:2];
NSLog(@"carC pass the road");
dispatch_semaphore_signal(semaphore);
});

//应用场景2 ：原子性保护，保证同时只有一个线程进入操作
//    dispatch_semaphore_t semaphore = dispatch_semaphore_create(1);
//    for(int i=0 ;i< 10000 ;i++){
//        dispatch_async(defaultQueue, ^{
//            dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
//            NSLog(@"i:%d",i);
//            dispatch_semaphore_signal(semaphore);
//        });
//    }

```

* GCD线程间的通信
其他线程执行完成，回到主线程
```
// 获取全局并发队列
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
// 获取主队列
dispatch_queue_t mainQueue = dispatch_get_main_queue();

dispatch_async(queue, ^{
// 异步追加任务 1
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程

// 回到主线程
dispatch_async(mainQueue, ^{
// 追加在主线程中执行的任务
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"2---%@",[NSThread currentThread]);      // 打印当前线程
});
});
```

* dispatch_barrier 栅栏（dispatch_barrier_async与dispatch_barrier_sync）
在执行完栅栏前面的操作之后，才执行栅栏操作，最后再执行栅栏后边的操作。
注意：在使用栅栏函数时，使用自定义队列才有意义。如果用的是串行队列或者系统提供的全局并发队列，这个栅栏函数的作用等同于一个同步函数的作用
```
- (void)barrierFunction {
NSLog(@"start");
dispatch_queue_t queue = dispatch_queue_create(DISPATCH_QUEUE_PRIORITY_DEFAULT, DISPATCH_QUEUE_CONCURRENT);
dispatch_sync(queue, ^{
// 追加任务 1
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程
});
dispatch_async(queue, ^{
// 追加任务 2
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"2---%@",[NSThread currentThread]);      // 打印当前线程
});

dispatch_barrier_async(queue, ^{
// 追加任务 barrier
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"barrier---%@",[NSThread currentThread]);// 打印当前线程
});
dispatch_sync(queue, ^{
// 追加任务 3
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"3---%@",[NSThread currentThread]);      // 打印当前线程
});
dispatch_async(queue, ^{
// 追加任务 4
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"4---%@",[NSThread currentThread]);      // 打印当前线程
});
dispatch_async(queue, ^{
// 追加任务 4
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"5---%@",[NSThread currentThread]);      // 打印当前线程
});
NSLog(@"end");

}
```
* dispatch_after 延时执行 
dispatch_after 方法并不是在指定时间之后才开始执行处理，而是在指定时间之后将任务追加到主队列中。严格来说，这个时间并不是绝对准确的。
```
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
    // 2.0 秒后异步追加任务代码到主队列，并开始执行
    NSLog(@"after---%@",[NSThread currentThread]);  // 打印当前线程
});
```

* 一次性代码 dispatch_once
即使在多线程的环境下，dispatch_once 也可以保证线程安全。
```
static dispatch_once_t onceToken;
dispatch_once(&onceToken, ^{
// 只执行一次的代码（这里默认是线程安全的）
});
```

* 快速迭代方法 dispatch_apply
如果是在串行队列中使用 dispatch_apply，那么就和 for 循环一样，按顺序同步执行。
如果在并发队列中进行异步执行，dispatch_apply可以在多个线程中同时遍历。但是dispatch_apply会在等待全部任务执行完毕，就像同步操作一样。
```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);

NSLog(@"apply---begin");
dispatch_apply(6, queue, ^(size_t index) {
NSLog(@"%zd---%@",index, [NSThread currentThread]);
});
NSLog(@"apply---end");
```

* GCD 队列组：dispatch_group
```
NSLog(@"group---begin");

dispatch_group_t group =  dispatch_group_create();

dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
// 追加任务 1
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程
});

dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
// 追加任务 2
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"2---%@",[NSThread currentThread]);      // 打印当前线程
});

dispatch_group_notify(group, dispatch_get_main_queue(), ^{
// 等前面的异步任务 1、任务 2 都执行完毕后，回到主线程执行下边任务
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"3---%@",[NSThread currentThread]);      // 打印当前线程

NSLog(@"group---end");
});
```

* dispatch_group_wait 阻塞（暂停）当前线程
等待指定的 group 中的任务执行完成后，才执行 dispatch_group_wait 之后的操作。
```
NSLog(@"group---begin");

dispatch_group_t group =  dispatch_group_create();

dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
// 追加任务 1
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程
});

dispatch_group_async(group, dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
// 追加任务 2
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"2---%@",[NSThread currentThread]);      // 打印当前线程
});

// 等待上面的任务全部完成后，会往下继续执行（会阻塞当前线程）
dispatch_group_wait(group, DISPATCH_TIME_FOREVER);

NSLog(@"group---end");
```

* dispatch_group_enter、dispatch_group_leave
dispatch_group_enter 标志着一个任务追加到 group，执行一次，相当于 group 中未执行完毕任务数 +1
dispatch_group_leave 标志着一个任务离开了 group，执行一次，相当于 group 中未执行完毕任务数 -1。
当 group 中未执行完毕任务数为0的时候，才会使 dispatch_group_wait 解除阻塞，以及执行追加到 dispatch_group_notify 中的任务。
```
NSLog(@"group---begin");

dispatch_group_t group = dispatch_group_create();
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_group_enter(group);
dispatch_async(queue, ^{
// 追加任务 1
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程

dispatch_group_leave(group);
});

dispatch_group_enter(group);
dispatch_async(queue, ^{
// 追加任务 2
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"2---%@",[NSThread currentThread]);      // 打印当前线程

dispatch_group_leave(group);
});

dispatch_group_notify(group, dispatch_get_main_queue(), ^{
// 等前面的异步操作都执行完毕后，回到主线程.
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"3---%@",[NSThread currentThread]);      // 打印当前线程

NSLog(@"group---end");
});
```

* dispatch_semaphore 信号量
Dispatch Semaphore 提供了三个方法：
dispatch_semaphore_create：创建一个 Semaphore 并初始化信号的总量
dispatch_semaphore_signal：发送一个信号，让信号总量加 1
dispatch_semaphore_wait：可以使总信号量减 1，信号总量小于 0 时就会一直等待（阻塞所在线程），否则就可以正常执行。
执行过程：dispatch_semaphore_wait 执行后，返回的信号量为 -1，判断 信号量 < 0 进入等待，执行 dispatch_semaphore_signal 之后，信号量 == 0 通过。
注意：信号量的使用前提是：想清楚你需要处理哪个线程等待（阻塞），又要哪个线程继续执行，然后使用信号量。

Dispatch Semaphore 在实际开发中主要用于：
保持线程同步，将异步执行任务转换为同步执行任务
保证线程安全，为线程加锁

`线程同步`：可理解为线程 A 和 线程 B 一块配合，A 执行到一定程度时要依靠线程 B 的某个结果，于是停下来，示意 B 运行；B 依言执行，再将结果给 A；A 再继续操作。
`线程安全`：如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。
若每个线程中对全局变量、静态变量只有读操作，而无写操作，一般来说，这个全局变量是线程安全的；若有多个线程同时执行写操作（更改变量），一般都需要考虑线程同步，否则的话就可能影响线程安全。

```
// 保持线程同步，将异步执行任务转换为同步执行任务
NSLog(@"semaphore---begin");

dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
dispatch_semaphore_t semaphore = dispatch_semaphore_create(0);

__block int number = 0;
dispatch_async(queue, ^{
// 追加任务
[NSThread sleepForTimeInterval:2];              // 模拟耗时操作
NSLog(@"1---%@",[NSThread currentThread]);      // 打印当前线程

number = 100;

dispatch_semaphore_signal(semaphore);
});

dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
NSLog(@"semaphore---end,number = %zd",number);
```

### NSOperation
基于GCD（底层是GCD），比GCD多了一些更简单实用的功能，更加面向对象。
相同的线程操作如果能用NSOperation操作就尽量用，不能实现的线程操作才使用GCD
* 优点：NSOperation任务可以被取消，GCD不可以。
* 缺点：
```
NSOperationQueue *queue = [[NSOperationQueue alloc]init];
//设置队列最大同时进行的任务数量，1为串行队列
[queue setMaxConcurrentOperationCount:1];
//添加一个block任务
[queue addOperationWithBlock:^{
sleep(2);
NSLog(@"block task 1");
}];
[queue addOperationWithBlock:^{
sleep(2);
NSLog(@"block task 2");
}];
//显示添加一个block任务
NSBlockOperation *block1 = [NSBlockOperation blockOperationWithBlock:^{
sleep(2);
NSLog(@"block task 3");
}];
//设置任务优先级
//说明：优先级高的任务，调用的几率会更大,但不表示一定先调用
[block1 setQueuePriority:NSOperationQueuePriorityHigh];
[queue addOperation:block1];

NSBlockOperation *block2 = [NSBlockOperation blockOperationWithBlock:^{
sleep(2);
NSLog(@"block task 4，任务3依赖4");
}];
[queue addOperation:block2];
//任务3依赖4
[block1 addDependency:block2];
//设置任务完成的回调
[block2 setCompletionBlock:^{
NSLog(@"block task 4 comlpete");
}];

//设置block1完成后才会继续往下走
[block1 waitUntilFinished];
NSLog(@"block task 3 is waitUntilFinished!");

//初始化一个子任务
NSInvocationOperation *oper1 = [[NSInvocationOperation alloc]initWithTarget:self selector:@selector(function1) object:nil];
[queue addOperation:oper1];

[queue waitUntilAllOperationsAreFinished];
NSLog(@"queue comlpeted");

//    取消全部操作
//    [queue cancelAllOperations];
//    暂停操作/恢复操作/是否暂定状态
//    [queue setSuspended:YES];[queue setSuspended:NO];[queue isSuspended];

//操作优先级

//      [queue waitUntilAllOperationsAreFinished];
```

* NSTimer
不支持多线程操作，但是可以执行异步操作，异步操作很方便，最常用的就是定时执行和延迟执行某一方法
```
//定时执行任务，可以重复和不重复
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:2 target:self selector:@selector(function1) userInfo:nil repeats:NO];

//暂时停止定时器
//[timer setFireDate:[NSDate distantFuture]];
//重新开启定时器
//[timer setFireDate:[NSDate distantPast]];
//永久通知定时器
//[timer invalidate];
//timer = nil;
```

## NSOperation&NSOperationQueue
### NSOperation
1.NSInvocationOperation
2.NSBlockOperation
3.自定义子类继承NSOperation
```
// 1.invocation
NSInvocationOperation *iop = [[NSInvocationOperation alloc]initWithTarget:self selector:@selector(test:) object:@"111"];
// 默认在当前线程同步执行
//    [iop start];
// 2.block
NSBlockOperation *bop = [NSBlockOperation blockOperationWithBlock:^{
[self test:@"222"];
}];
[bop addExecutionBlock:^{
[self test:@"333"];
}];
[bop start];
```

### NSOperationQueue
```
NSOperationQueue *queue1 = [[NSOperationQueue alloc]init];
queue1.maxConcurrentOperationCount = 1;
// 追加任务 方式一
[queue1 addOperation:iop];
// 追加任务 方式二
[queue1 addOperationWithBlock:^{
[self test:@"444"];
}];
// 线程取消
//    [iop cancel];
//    [queue1 cancelAllOperations];
// 线程暂停/恢复
//    [queue1 setSuspended:YES];
// 线程监听
//    [bop setCompletionBlock:^{
//        [self test:@"555"];
//    }];
```

### 操作依赖
```
// 操作A执行完后，才能执行操作B
[operationB addDependency:operationA]; // 操作B依赖于操作A
```

## 线程间通信
### perfomSelector
```
// 在子线程中调用download方法下载图片
[self performSelectorInBackground:@selector(download) withObject:nil];
// 回到主线程中设置图片
[self performSelectorOnMainThread:@selector(settingImage:) withObject:image waitUntilDone:NO];
```



### GCD
```
//获取一个全局的队列
dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
//先开启一个线程，把下载图片的操作放在子线程中处理
dispatch_async(queue, ^{
    //回到主线程刷新UI
    dispatch_async(dispatch_get_main_queue(), ^{

    });
});
```

## 线程安全
### @synchronized
互斥锁：@synchronized(锁对象) { // 需要锁定的代码  }
```
//售票
-(void)saleTicket
{
    while (1) {

        //加互斥锁
        @synchronized(self) {
            [NSThread sleepForTimeInterval:0.03];
            //1.先查看余票数量
            NSInteger count = self.totalticket;

            if (count >0) {
                self.totalticket = count - 1;
                NSLog(@"%@卖出去了一张票,还剩下%zd张票",[NSThread currentThread].name,self.totalticket);
            }else
            {
                NSLog(@"%@发现当前票已经买完了--",[NSThread currentThread].name);
                break;
            }
        }
    }
}
```

### 信号量
```
semaphoreLock = dispatch_semaphore_create(1);

- (void)saleTicket {

    while (1) {
        // 相当于加锁
        dispatch_semaphore_wait(semaphoreLock, DISPATCH_TIME_FOREVER);

        if (self.ticketSurplusCount > 0) {  // 如果还有票，继续售卖
            self.ticketSurplusCount--;
            NSLog(@"%@", [NSString stringWithFormat:@"剩余票数：%d 窗口：%@", self.ticketSurplusCount, [NSThread currentThread]]);
            [NSThread sleepForTimeInterval:0.2];
        } else { // 如果已卖完，关闭售票窗口
            NSLog(@"所有火车票均已售完");

            // 相当于解锁
            dispatch_semaphore_signal(semaphoreLock);
            break;
        }

        // 相当于解锁
        dispatch_semaphore_signal(semaphoreLock);
    }
}
```
