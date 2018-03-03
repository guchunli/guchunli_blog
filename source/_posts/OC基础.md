---
title: OC基础
date: 2018-03-01 13:54:16
categories: 学习
tags: [OC]
toc: true
---

# 基础
## 运算符优先级
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

### 内存管理规律
1.MRC：当调用这个对象的alloc、retain、new、copy方法之后引用计数器自动在原来的基础上加1（ObjC中调用一个对象的方法就是给这个对象发送一个消息），当调用这个对象的release，autorelease方法之后它的引用计数器减1，如果一个对象的引用计数器为0，则系统会自动调用这个对象的dealloc方法来销毁这个对象。
2.ARC：编译器会管理好对象的内存，会在何时的地方插入retain, release和autorelease。
ARC的判断准则：ARC判断一个对象是否需要释放不是通过引用计数来进行判断的，而是通过强指针来进行判断的。只要还有一个强指针变量指向对象，对象就会保持在内存中, 否则就会被释放。

* 强指针/弱指针
强指针：被__strong修饰的指针，默认所有对象的指针变量都是强指针
弱指针：被__weak修饰的指针
```
__strong  Person *p1 = [[Person alloc] init];
__weak  Person *p2 = [[Person alloc] init];
```

（1）单个对象内存管理规律
* 局部变量释放，对象被释放
* 清空指针，对象被释放
* 不要使用弱指针保存新创建的对象，否则对象会被立即释放
（2）多个对象内存管理规律

### AutoreleasePool
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
只是把对release的调用延迟了，调用完autorelease方法后，对象的计数器不变

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

* 栈/堆
栈：由操作系统自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈(先进后出)，非OC对象一般放在操作系统的栈里面。
堆：一般由程序员分配释放，若程序员不释放，程序结束时可能由OS回收，分配方式类似于链表，继承了NSObject的对象的存储在操作系统的堆里边。

* NSString *name，非ARC下重写setter,getter方法
`一旦重写了getter.setter方法,必须使用@synthesize variable = _variable来区分属性名与方法名.`
```
-(void)setName:(NSString *)name{
    if (_name != name) {    //首先判断要赋值的变量和当前成员变量是不是同一个变量
    [_name release];        //释放之前的对象
    _name = [name retain];    //赋值时重新retain
    }
}
- (NSString *)name{
    return [[_name retain]autorelease];
}
```


## 多线程
### 进程/线程的区别？同步/异步的区别？并行/并发的区别？
进程：有独立的地址空间，进程中包含的一个或多个执行单元称为线程。
线程：有自己的堆栈和局部变量，一个线程死掉就等于整个进程死掉。

* 同步和异步的区别：请求发出后，是否需要等待结果，才能继续执行其他操作。
同步：指那些需要其他端协作或者需要一定时间完成的任务，发出一个功能调用时，在没有得到结果之前，该调用就不返回或继续执行后续操作。（同步就是必须一件一件事做，等前一件做完了才能做下一件事。）
异步：当一个异步过程调用发出后，调用者在没有得到结果之前，就可以继续执行后续操作。
阻塞：
非阻塞：

* 并行和并发的区别：`是否是『同时』`
并行:两个或多个事件在同一时刻发生。（`你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你支持并行。`）
关键是你有`同时`处理多个任务的能力。
并发:两个或者多个事件在同一时间间隔发生。（`你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你支持并发。`）
关键是你有处理多个任务的能力，不一定要同时。

### 多线程下，NSMutableArray需不需要开启线程保护

### 线程间通信

### 线程同步
1.dispatch_group 线程组
2.dispatch_barrier
3.dispatch_semaphore 信号量

## 锁（解决抢夺资源问题）
1.@synchronized
```
@synchronized(self)
{
// 这段代码对其他 @synchronized(self) 都是互斥的
// self 指向同一个对象
}
```

2.NSLock
```
NSLock *theLock = [[NSLock alloc] init];
if ([theLock lock])
{
//do something here
[theLock unlock];
}
```

3.NSRecursiveLock 递归锁
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

4.NSConditionLock 条件锁
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

5.分布锁
文件方式实现，可以跨进程
用tryLock方法获取锁。
用unlock方法释放锁。
如果一个获取锁的进程在释放锁之前挂了，那么锁就一直得不到释放了，此时可以通过breakLock强行获取锁。

## 数据持久化
### plist文件（属性列表）
### preference（偏好设置）
### NSKeyedArchiver（归档）
### SQLite 3
### CoreData

## 网络请求

### SDWebImage缓存机制

### NSCache优于NSDictionary的地方
1.当系统资源将要耗尽时，NSCache可以自动删减缓存
2.NSCache是线程安全的，而NSDictionary则绝对不具备此优势

## 设计模式

### block

### 为什么代理用weak，delegate和dataSource有什么区别，delegate和block的区别
1.weak指明该对象不会保持delegate，delegate这个对象的销毁由外部控制，strong会强引用delegate，外界不能销毁delegate，会导致循环引用。
2.datasource是关于数据的，都有哪些属性，值等；delegate时关于操作的，有什么方法可以调用。
3.delegate和block都可以回调。delegate是个对象，调用代理协议函数完成操作。block是传递一个函数指针，利用函数指针执行来进行回调。内存管理上，delegate不需要保存引用，block对数据又copy的处理。

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

### 函数式，响应式编程


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

# 中级

## 动画

## xib,storyboard,autolayout
缺点：
1.难以维护
2.性能
3.错误定位困难

## application，view／controller加载顺序

## runtime

## runloop

## 类结构

## UICollectionViewLayout
<!--调用顺序：-->
<!--1.prepareLayout-->
<!--2.-(CGSize) collectionViewContentSize-->
<!--3.-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect-->




# 高级

## iOS各版本差异

## 性能优化，内存泄漏

## git

## cocoapods私有库

## 原生与js交互

## openGL

## 音视频播放，直播，ffmpeg

## swift

## react native



参考文章：[2017年5月iOS招人心得答案总结](https://www.jianshu.com/p/7d486b24dc21)
[关于NSMutableArray线程安全的思考和实现](http://blog.csdn.net/kongdeqin/article/details/53171189)
