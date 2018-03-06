---
title: OC基础
date: 2018-03-01 13:54:16
categories: 学习
tags: [OC]
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

## 属性
### @property本质
1.属性的实质：ivar实例变量+getter+setter
```
property在runtime中是objc_property_t，是一个结构体
struct property_t {
const char *name;
const char *attributes;
};

而attributes本质是objc_property_attribute_t，定义了property的一些属性，attributes具体包括：类型，原子性，内存语义和对应的实例变量。定义如下：
typedef struct {
const char *name;           /**< The name of the attribute */
const char *value;          /**< The value of the attribute (usually empty) */
} objc_property_attribute_t;

@property (nonatomic, copy) NSString *string;，通过 property_getAttributes(property)获取到attributes并打印出来之后的结果为：T@"NSString",C,N,V_string，T就代表类型，C就代表Copy，N代表nonatomic，V就代表对应的实例变量
```

2.ivar、getter、setter 是如何生成并添加到这个类中的：自动合成。完成属性定义后，编译器会自动写出一套存取方法，用以访问给定类型中具有给定名称的变量，这个过程由编译 器在编译期执行。 所以也可以这么说：@property = getter + setter。

### 属性关键字
* 默认值：atomic，readwrite，assign/strong(基本数据类型／对象)
1.原子性：atomic：通过锁定机制确保其原子性，提供线程安全，耗费系统资源，使用了`同步锁`
nonatomic：非原子性，不提供线程安全，效率更高，不使用自旋锁，如果其中一个线程正在改写属性值，另外一个线程会把尚未修改好的属性值读取处理。
* atomic是绝对的线程安全吗？
NO。例如：一个线程在连续多次读取属性值的过程中有别的线程在同时改写该值，那么即使将属性声明为atomic，还是会读取到不同的值。atomic仅仅是读写安全，但并不是线程安全的，因为别的线程还能进行读写之外的其他操作。线程安全需要开发者自己来保证。如果有线程C在A线程读操作之前release了该属性，那么还会导致程序崩溃。比如用atomic去操作一个NSMutableArray ，如果一个线程循环读数据，一个线程循环写数据，肯定会产生内存问题。
* atomic/nonatomic的区别
nonatomic:
```
- (void)setCurrentImage:(UIImage *)currentImage
{
    if (_currentImage != currentImage) {
        [_currentImage release];
        _currentImage = [currentImage retain];
    }
}

- (UIImage *)currentImage
{
    return _currentImage;
}
```

atomic:
```
- (void)setCurrentImage:(UIImage *)currentImage
{
    @synchronized(self) {
        if (_currentImage != currentImage) {
            [_currentImage release];
            _currentImage = [currentImage retain];
        }
    }
}

- (UIImage *)currentImage
{
    @synchronized(self) {
        return _currentImage;
    }
}
```

2.读写权限：readonly只读，readwrite读写

3.内存管理语义：
strong：在赋值时调用被指向对象的retain方法，使其引用计数+1
weak：不持有对象，不增加对象的引用计数，对象消失后，指针自动置nil，只适用于对象
unsafe_unretained：与weak类似，引用计数为0时，不会置nil
assign：不更改引用计数，适用于基础数据类型／对象
copy：建立一个引用计数为1的对象，与 strong 类似，然而在设置新属性值时并不保留新值，而是拷贝一份

4.方法名：getter=<name>、setter=<name>
```
//getter
@property (nonatomic, getter=isOn) BOOL on;
```

5.不常用：nonnull,null_resettable,nullable

* @synthesize：编译期间，让编译器自动生成getter／setter方法，当有自定义的存或取方法时屏蔽自动生成的方法
* @dynamic：不自动生成getter／setter方法，避免编译期间产生警告，自己实现存取方法

#### assign/weak
* 用assign声明的变量在栈中可能不会自动赋值为nil，就会造成野指针错误
* 用weak声明的变量在栈中会自动清空，赋值为nil
assigin 可以用非OC对象,而weak必须用于OC对象

#### copy
1.使用场景
* NSString、NSArray、NSDictionary 等等经常使用copy关键字，是因为他们有对应的可变类型：NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进行赋值操作，为确保对象中的字符串值不会无意间变动，应该在设置新属性值时拷贝一份“不可变” (immutable)的字符串，用此特质来保护其`封装性`。
* 用于block：编译器会自动对 block 进行了 copy 操作。

2. `@property (copy) NSMutableArray *array;`会有什么问题：1、添加,删除,修改数组内的元素的时候,程序会因为找不到对应的方法而崩溃.因为 copy 就是复制一个不可变 NSArray 的对象；2、使用了 atomic 属性会严重影响性能 。

3.如何使自己的类用copy修饰符，如何重写带copy关键字的setter
实现NSCopying协议，如果自定义的对象分为可变版本与不可变版本，那么就要同时实现 NSCopying 与 NSMutableCopying 协议。
NSCopying协议：`- (id)copyWithZone:(NSZone *)zone;`

重写带copy关键字的setter：
```
- (id)copyWithZone:(NSZone *)zone {
User *copy = [[[self class] allocWithZone:zone]
init];
copy.name = _name;
copy.age = _age;
copy.sex = _sex;
return copy;
}

- (void)setName:(NSString *)name {
    //[_name release];
    _name = [name copy];
}
```

4.浅复制／深复制
浅复制：指针复制，并没有进行对象复制;
单层深复制：也就是我们经常说的深复制，我这里说的单层深复制是对于集合类所说的(即NSArray,NSDictionary,NSSet)，单层深复制指的是只复制了该集合类的最外层，里边的元素没有复制，(即这两个集合类的地址不一样，但是两个集合里所存储的元素的地址是一样的);
完全复制：指的是完全复制整个集合类，也就是说两个集合地址不一样，里边所存储的元素地址也不一样;

区别：
(1)非集合类（NSString，NSNumber）
```
[immutableObject copy]  //浅复制
[immutableObject mutableCopy] //深复制
[mutableObject copy] //深复制
[mutableObject mutableCopy] //深复制
```

结论：不可变进行copy是浅复制，mutableCopy是深复制，可变的copy，mutableCopy都是深复制

(2)集合类（NSArray，NSDictionary, NSSet)
```
[immutableObject copy]  //浅复制
[immutableObject mutableCopy] //单层深复制
[mutableObject copy] //单层深复制
[mutableObject mutableCopy] //单层深复制
```

结论：不可变进行copy是浅复制，mutableCopy是单层深复制，如何实现完全复制?
```
NSArray *copyArray = [[NSArray alloc] initWithArray:array copyItems:YES];  // 完全复制
```

对于自定义继承自NSObject的类:
`copy`需要实现`NSCopying`协议，然后实现`NSCopying`协议方法，否则copy会crash；
`mutableCopy`时，需要实现`NSMutableCopying`协议，否则mutableCopy会crash。

#### weak
1.为什么IBOutlet修饰的UIView也适用weak关键字
UIViewController->UIView->subView->UIButton
view对加到它上面的控件是强引用，在viewController里面使用Outlet属性，仅仅是对其使用，并没有必要拥有它，strong也不会造成强引用循环，不管声明的属性是强引用还是弱引用，在控制器消失的时候，这个属性消失，View消失，subViews消失，控件也就消失了。
手动创建控件：
```
//将控件声明成strong
_btn = [[UIButton alloc]init];
[self.view addSubview:_btn]

//将控件声明成weak
UIButton *button = [[UIButton alloc]init];
_btn = button;
[self.view addSubview:_btn];
```

2.runtime 如何实现 weak 属性
为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同 assign 类似， 然而在属性所指的对象遭到摧毁时，属性值也会清空(nil out)。
runtime 对注册的类， 会进行布局，对于 weak 对象会放入一个 hash 表中。 用 weak 指向的对象内存地址作为 key，当此对象的引用计数为0的时候会 dealloc，假如 weak 指向的对象内存地址是a，那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从而设置为 nil。

## OC的内存管理机制
MRC(manual retain-release)手动内存管理
ARC(automatic reference counting)自动引用计数： 在iOS5.0 (Xcode4) 版本后推出的。
Garbage collection (垃圾回收)：iOS不支持

* 栈/堆
栈：由操作系统自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈(先进后出)，非OC对象一般放在操作系统的栈里面。
堆：一般由程序员分配释放，若程序员不释放，程序结束时可能由OS回收，分配方式类似于链表，继承了NSObject的对象的存储在操作系统的堆里边。
`任何继承了NSObject的对象需要进行内存管理，非对象类型(int、char、float、double、struct、enum等) 不需要进行内存管理`

### 内存管理规律
1.MRC：当调用这个对象的alloc、new、retain、copy、mutableCopy方法之后引用计数器自动在原来的基础上加1（ObjC中调用一个对象的方法就是给这个对象发送一个消息），当调用这个对象的release，autorelease方法之后它的引用计数器减1，如果一个对象的引用计数器为0，则系统会自动调用这个对象的dealloc方法来销毁这个对象。
2.ARC：编译器会管理好对象的内存，会在何时的地方插入retain, release和autorelease。
ARC的判断准则：ARC判断一个对象是否需要释放不是通过引用计数来进行判断的，而是通过强指针来进行判断的。只要还有一个强指针变量指向对象，对象就会保持在内存中, 否则就会被释放。

* 强指针/弱指针
强指针：被__strong修饰的指针，默认所有对象的指针变量都是强指针
弱指针：被__weak修饰的指针，不要使用弱指针保存新创建的对象，否则对象会被立即释放
```
__strong  Person *p1 = [[Person alloc] init];
__weak  Person *p2 = [[Person alloc] init];
```

### AutoreleasePool（队列）
* 只要给对象发送一条autorelease消息，会将对象放到一个自动释放池中，当该pool被释放时,该pool中的所有对象会被调用一次release。
* 动释放池是以栈的形式存在，栈顶就是离调用autorelease方法最近的自动释放池

* AutoreleasePool的使用
注意：放到自动释放池代码中的对象，只有调用了 autorelease 方法，对象才会加入到自动释放池
```
NSAutoreleasePool *autoreleasePool = [[NSAutoreleasePool alloc] init];
Person *p = [[[Person alloc] init] autorelease];
[autoreleasePool drain];
```

```
@autoreleasepool
{ // 开始代表创建自动释放池
Person *p = [[Person new] autorelease];
// 将代码写到这里就放入了自动释放池
} // 结束代表销毁自动释放池(会给池子中所有对象发送一条release消息)
```


* autorelease
返回对象本身，只是把对release的调用延迟了，调用完autorelease方法后，对象的计数器不变

* dealloc
```
-(void)dealloc{
NSLog(@"Invoke Person's dealloc method.");
[super dealloc];//注意：super dealloc一定要写到所有代码的最后（两个目的：一是父类可能有其他引用对象需要释放；二是：当前对象真正的释放操作是在super的dealloc中完成的）
}
```

* 野指针/空指针
野指针：一个指针指向一个僵尸对象（被释放的对象），给一个野指针发送消息就会报错(EXC_BAD_ACCESS错误)
空指针：没有指向存储空间的指针(里面存的是nil, 也就是0)，给空指针发消息是没有任何反应的
```
//如果不设置p=nil，则p就是一个野指针,此时如果再调用对象release会报错
//但是如果此时p已经是空指针了，则在ObjC中给空指针发送消息是不会报错的
p=nil;
[p release];
```

* NSString *name，非ARC下重写setter,getter方法
`一旦重写了getter.setter方法,必须使用@synthesize variable = _variable来区分属性名与方法名.`
```
-(void)setName:(NSString *)name{
    if (_name != name) {    //首先判断要赋值的变量和当前成员变量是不是同一个变量
    [_name release];        //释放之前的对象(换房间操作，释放之前的房间)
    _name = [name copy];    //赋值时重新retain（对房间进行持有）
    }
}
- (NSString *)name{
    return [[_name retain]autorelease];
}
```


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
全局并发队列：global
主队列：系统给每一个应用程序提供了三个concurrent dispatch queues，主队列中的任务都会在主线程中执行

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

## 设计模式
### block
实质是OC对象

* 为什么在默认情况下无法修改被block捕获的变量？
Block只捕获Block中会用到的变量。由于只捕获了自动变量(自动变量是以值传递方式传递到Block的构造函数里面)的值，并非内存地址，所以Block内部不能改变自动变量的值。Block捕获的外部变量可以改变值的是静态变量，静态全局变量，全局变量。

#### 函数指针与指针函数

### 为什么代理用weak，delegate和dataSource有什么区别，delegate和block的区别
1.weak指明该对象不会保持delegate，delegate这个对象的销毁由外部控制，strong会强引用delegate，外界不能销毁delegate，会导致循环引用。
2.datasource是关于数据的，都有哪些属性，值等；delegate时关于操作的，有什么方法可以调用。
3.delegate和block都可以回调。delegate是个对象，调用代理协议函数完成操作。block是传递一个函数指针，利用函数指针执行来进行回调。内存管理上，delegate不需要保存引用，block对数据又copy的处理。

### KVO


### delegate

### 通知

#### delegate与通知的区别
* 协议有控制链(has-a)的关系，通知没有。
* 通知：一对多。代理/block：一对一。
* OC中的多继承用委托代理实现

### 单例

### MVC/MVP/MVVM
1.MVC
视图（view）：用户界面
模型（model）：数据保存
控制器（controller）：业务逻辑

通信方式：view->controller->model->view

2.MVP
将controller改名为presenter
各部分之间的通信都是双向的，view与model不发生联系，都通过presenter传递，view薄，controller厚。

通信方式：view<- &->presenter<- &->model

3.MVVM
将presenter改名为viewmodel
双向绑定，view的变动自动反应在viewmodel，反之亦然。

通信方式：view<->viewmodel<- &->model

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

## 动画

## xib,storyboard,autolayout
缺点：
1.难以维护
2.性能
3.错误定位困难

## 类结构

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

## UITableview的优化方法
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

## iOS各版本差异
### iOS9
1.UIStackView

2.ATS

### iOS11


## 性能优化，内存泄漏
### time profile

### leaks

## git

## 原生与js交互

## RAC 函数式，响应式编程

## 动画效果

### 组件化

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

## react native

参考文章：[2017年5月iOS招人心得答案总结](https://www.jianshu.com/p/7d486b24dc21)
[关于NSMutableArray线程安全的思考和实现](http://blog.csdn.net/kongdeqin/article/details/53171189)
[OC知识--彻底理解内存管理(MRC、ARC)](https://www.jianshu.com/p/48665652e4e4)
