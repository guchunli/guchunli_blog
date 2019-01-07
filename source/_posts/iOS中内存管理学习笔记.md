---
title: iOS中内存管理学习笔记
date: 2018-06-25 16:20:20
categories: 笔记
tags: [面试]
toc:
---


## 内存空间
`任何继承了NSObject的对象需要进行内存管理，非对象类型(int、char、float、double、struct、enum等) 不需要进行内存管理`
内存（RAM）中的5大区都是什么？
* 栈区（stack）：由操作系统自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈(先进后出)，非OC对象一般放在操作系统的栈里面。
* 堆区（heap）：一般由程序员分配释放，若程序员不释放，程序结束时可能由OS回收，分配方式类似于链表，继承了NSObject的对象的存储在操作系统的堆里边。
* 全局区（静态区）（static）：全局变量和静态变量的存储是放在一块的，初始化的全局变量和静态变量在一块区域，未初始化的全局变量和未初始化的静态变量在相邻的另一块区域。程序结束后由系统释放。
* 文字常量区：常量字符串就是放在这里的，还有const常量。程序结束后由系统释放。
* 程序代码区：存放函数体的二进制代码。

## MRC
MRC：当调用这个对象的alloc、new、retain、copy、mutableCopy方法之后引用计数器自动在原来的基础上加1（ObjC中调用一个对象的方法就是给这个对象发送一个消息），当调用这个对象的release，autorelease方法之后它的引用计数器减1，如果一个对象的引用计数器为0，则系统会自动调用这个对象的dealloc方法来销毁这个对象。

| 对象操作 | Objective-C方法 |    对应的操作结果  |
|:--------:|:------:|----------:|
| 生成并持有对象  |  alloc, new, copy,mutableCopy等方法  |  生成对象并设置引用计数 =1 |
| 持有对象  |  reatain方法  |  使引用计数 +1 |
| 释放对象  |  release方法  |  使引用计数 -1 |
| 废弃对象  |  dealloc方法  |  引用计数 = 0 时调用（系统自动调用） |
<!--more-->
```
+(id)alloc
{
return [self allocWithZone:NSDefaultMallocZone()];
}
+(instancetype)allocWithZone:(struct _NSZone *)zone
{
//NSAllocateObject方法开辟了一块内存空间
return NSAllocateObject(self, 0, zone);
}
```

### new与alloc/init
两种方式创建对象现在基本上一样，区别就是使用new只能默认init进行初始化，alloc方式可以使用其它的init开头的方法进行初始化。

### dealloc
当一个对象的引用计数为0的时候,也就意味着没有任何地方需要该对象,系统会自动回收对该对象所占用的内存,在系统销毁对象的时候,会自动调用该对象的 dealloc 方法来执行一些回收的操作,如果此时该对象还对其他对象有引用的话,那么就需要重写 dealloc 方法来释放该对象对其他对象的引用 以确保该对象能正常释放销毁
重写 dealloc 方法:
```
- (void)dealloc {

// 处理该对象的其他引用(通过release方法)

/** 回调父类的dealloc方法 */
[super dealloc];
}
```

## ARC
当调用这个对象的alloc、new、retain、copy、mutableCopy方法之后引用计数器自动在原来的基础上加1（ObjC中调用一个对象的方法就是给这个对象发送一个消息），当调用这个对象的release，autorelease方法之后它的引用计数器减1，如果一个对象的引用计数器为0，则系统会自动调用这个对象的dealloc方法来销毁这个对象。
当一个autorelease pool(自动释放池)被drain(销毁)的时候会对pool里的对象发送一条release的消息.
* 在ARC项目中我们同样可以创建NSAutoreleasePool类对象去帮助我们更精确的管理内存问题。
* NSAutoreleasePool的管理范围是在NSAutoreleasePool *pool = [[NSAutoreleasePool alloc]init];与[pool release];之间的对象
* 既然ARC项目中设置了ARC，为什么还要使用@autoreleasepool?（注意a的案例解释）ARC 并不是舍弃了@autoreleasepool，而是在编译阶段帮你插入必要的 retain/release/autorelease的代码调用。所以，跟你想象的不一样，ARC 之下依然是延时释放的，依然是依赖于 NSAutoreleasePool，跟非 ARC模式下手动调用那些函数本质上毫无差别，只是编译器来做会保证引用计数的正确性
* NSAutoreleasePool *pool=[[NSAutoreleasePool alloc] init]; 当执行[pool autorelease]的时候，系统会进行一次内存释放，把autorelease的对象释放掉，如果没有NSAutoreleasePool, 那这些内存不会释放
注意，对象并不是自动被加入到当前pool中，而是需要对对象发送autorelease消息，这样，对象就被加到当前pool的管理里了。当当前pool接受到drain消息时，它就简单的对它所管理的所有对象发送release消息。
* 在ARC项目中.不能直接使用autorelease pools,而是使用@autoreleasepool{},@autoreleasepool{}比直接使用NSAutoreleasePool效率高。不使用ARC的时候也可以使用(autorelease嵌套）

### AutoreleasePool
* 只要给对象发送一条autorelease消息，会将对象放到一个自动释放池中，当该pool被释放时,该pool中的所有对象会被调用一次release。
* 自动释放池是以栈的形式存在，栈顶就是离调用autorelease方法最近的自动释放池

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

* autoReleasePool 什么时候释放?
App启动后，苹果在主线程 RunLoop 里注册了两个 Observer，其回调都是 _wrapRunLoopWithAutoreleasePoolHandler()。
第一个 Observer 监视的事件是 Entry(即将进入Loop)，其回调内会调用 _objc_autoreleasePoolPush() 创建自动释放池。其 order 是 -2147483647，优先级最高，保证创建释放池发生在其他所有回调之前。
第二个 Observer 监视了两个事件： BeforeWaiting(准备进入休眠) 时调用_objc_autoreleasePoolPop()  和 _objc_autoreleasePoolPush() 释放旧的池并创建新池；Exit(即将退出Loop) 时调用 _objc_autoreleasePoolPop() 来释放自动释放池。这个 Observer 的 order 是 2147483647，优先级最低，保证其释放池子发生在其他所有回调之后。
在主线程执行的代码，通常是写在诸如事件回调、Timer回调内的。这些回调会被 RunLoop 创建好的 AutoreleasePool 环绕着，所以不会出现内存泄漏，开发者也不必显示创建 Pool 了。

* autorelease
返回对象本身，只是把对release的调用延迟了，调用完autorelease方法后，对象的计数器不变

* drain
销毁一个自动释放池.

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

### ARC下的dealloc
ARC下,系统可以帮我们释放该对象，及其包含的对象，但是却无法释放不属于该对象的一些东西。
* 移除通知的观察者,或KVO的观察者
* 对象强委托/引用的解除(例如XMPPMannerger的delegateQueue)
* 做一些其他的注销之类的操作(关闭程序运行期间没有关闭的资源)


## block使用时的一些情况以及防止循环引用
* block 在实现时就会对它引用到的它所在方法中定义的栈变量进行一次只读拷贝，然后在 block 块内使用该只读拷贝。
```
-(void)testVariable
{
    NSInteger outsideVariable = 10;
    // __block NSInteger outsideVariable = 10;
    NSMutableArray * outsideArray = [[NSMutableArray alloc] init];
    void (^blockObject)(void) = ^(void){
        NSInteger insideVariable = 20;
        NSLog(@"  > member variable = %d", self.memberVariable);
        NSLog(@"  > outside variable = %ld", (long)outsideVariable);
        NSLog(@"  > inside variable = %ld", (long)insideVariable);

        [outsideArray addObject:@"AddedInsideBlock"];
    };

    outsideVariable = 30;
    self.memberVariable = 30;

    blockObject();

    NSLog(@"  > %lu items in outsideArray", (unsigned long)[outsideArray count]);
}

> member variable = 30
> outside variable = 10 //>使用__block修饰则输出 outside variable = 30 
> inside variable = 20
> 1 items in outsideArray
```
* 局部变量：blockObject 在实现时会对 outside 变量进行只读拷贝，在 block 块内使用该只读拷贝。因此这里输出的是拷贝时的变量值 10。如果，我们想要让 blockObject 修改或同步使用 outside 变量就需要用 __block 来修饰 outside 变量。
* static 变量，全局变量：在 block 中是有读写权限的，因为在 block 的内部实现中，拷贝的是指向这些变量的指针。
* 数组：我们往 outsideArray 数组中添加了值，但并未修改 outsideArray 自身，这是允许的，因为拷贝的是 outsideArray 自身。
* __block：__block 变量的内部实现要复杂许多，__block 变量其实是一个结构体对象，拷贝的是指向该结构体对象的指针。

### __strong/__weak/__unsafe_unretain/__autoreleasing
* 强指针/弱指针
强指针：被__strong修饰的指针，默认所有对象的指针变量都是强指针
弱指针：被__weak修饰的指针，不要使用弱指针保存新创建的对象，否则对象会被立即释放
```
__strong  Person *p1 = [[Person alloc] init];
__weak  Person *p2 = [[Person alloc] init];
```

#### __weak
如果局部变量block中retain了self，当block中的代码被执行完后，self就会被ARC释放。所以不需要处理weakself的情况。
```
NSArray *anArray = @[@"1", @"2", @"3"];
[anArray enumerateObjectsUsingBlock:^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
    [self doSomething:obj];
}];
```

如果block被self strong引用。所以结果就是block中引用了self，self引用了block。那么这个时候，如果你不使用weakself，则self和block永远都不会被释放。
```
__weak SecondViewController *weakself = self;
self.aBlock = ^(id  _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop){
[weakself doSomething:idx];
};
```

#### 强弱引用转换
强弱引用转换，用于解决代码块（block）与强引用self之间的循环引用问题
```
#ifndef    weakify_self
#if __has_feature(objc_arc)
#define weakify_self autoreleasepool{} __weak __typeof__(self) weakSelf = self;
#else
#define weakify_self autoreleasepool{} __block __typeof__(self) blockSelf = self;
#endif

#endif
#ifndef    strongify_self
#if __has_feature(objc_arc)
#define strongify_self try{} @finally{} __typeof__(weakSelf) self = weakSelf;
#else
#define strongify_self try{} @finally{} __typeof__(blockSelf) self = blockSelf;
#endif
#endif
```

调用方式：`@weakify_self`实现弱引用转换，`@strongify_self`实现强引用转换
```
@property (nonatomic, copy) void(^aBlock)(NSUInteger idx);

@weakify_self
self.aBlock = ^(NSUInteger idx){
    [weakSelf doSomething:idx];
};

@weakify_self
self.aBlock = ^(NSUInteger idx){
    @strongify_self
    [self doSomething:idx];
};
```

* __strong关键字与retain关似，用了它，引用计数自动＋1
* __autoreleasing
表示在autorelease pool中自动释放对象的引用
```
__autoreleasing NSString *str;
@autoreleasepool {
str = [NSString stringWithFormat:@"sunnyxx"];
}
NSLog(@"%@", str); // Console: (null)
```

* __weak：若附有__weak修饰符的变量所引用的对象被废弃,则将 nil赋值给该变量，使用附有__weak修饰符的变量,即是使用注册到__autoreleasepool 中的对象

* __autoreleasing：将对象赋值给附有__autoreleasing修饰的变量等同于 ARC无效时调用对象的 autorelease 方法

## enumerateObjectsUsingBlock
```
[array enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
// 这里被一个局部@autoreleasepool包围着
}];

NSDictionary * dic = [NSDictionary dictionaryWithObjectsAndKeys:@"obj1",@"key1",@"obj2",@"key2", nil];
[dic enumerateKeysAndObjectsUsingBlock:^(id key, id value, BOOL *stop) {
NSLog(@"value for key %@ is %@ ", key, value);
if ([@"key2" isEqualToString:key]) {
*stop = YES;    //当需要结束循环的时候,调用stop,赋予YES
}
}];
```

for in、经典for循环和EnumerateObjectsUsingBlock 的比较：
* 对于集合中对象数很多的情况下，for in 的遍历速度非常之快，但小规模的遍历并不明显（还没普通for循环快）
* Value查询index的时候, 面对大量的数组推荐使用 enumerateObjectsWithOptions的并行方法.
* 遍历字典类型的时候, 推荐使用enumerateKeysAndObjectsUsingBlock,block版本的字典遍历可以同时取key和value（forin只能取key再手动取value）

遍历数组的同时删除元素：
如果在for in 循环里，对这个数组进行了修改的话，无论是增，删，修改数组元素位置，都会扔一个异常出来，枚举的过程中数组发生了突变（<__NSArrayM: 0xa4fc000> was mutated while being enumerated.），但是如果写成for循环或Enumerate都没有问题。

* NSString *name，非ARC下重写setter,getter方法
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

[block使用时的一些情况以及防止循环引用](https://blog.csdn.net/cyj_sky/article/details/51442732)

