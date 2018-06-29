---
title: iOS runtime与runloop学习笔记
date: 2017-03-22 11:39:18
categories: 笔记
tags: [iOS,runtime]
toc: true
---

# runtime
Objective-C 是一门动态语言，它把很多静态语言在编译和链接时做的事情放到了运行时去处理，它在运行时实现了对类、方法、成员变量、属性等信息的管理机制。
* 需要`#import <objc/runtime.h>`

* 基于C，为C添加类面向对象的特性。
对象方法：保存到类对象的方法列表。
类方法：保存到元类(Meta Class)的方法列表。

<!--more-->
## 运行时的类与对象
类与对象相关的函数
```
const char *class_getName(Class cls)，获取指定类的类名。
BOOL class_isMetaClass(Class cls)，判断指定类是否是一个元类。
Class class_getSuperclass(Class cls)，获取指定类的父类。
Class class_setSuperclass(Class cls, Class newSuper)，设定指定类的父类。
int class_getVersion(Class cls)，获取指定类的版本信息。
void class_setVersion(Class cls, int version)，设定指定类的版本信息。
size_t class_getInstanceSize(Class cls)，获取实例大小。
Ivar class_getInstanceVariable(Class cls, const char *name)，获取指定名字的实例变量。
Ivar class_getClassVariable(Class cls, const char *name)，获取指定名字的类变量。
Ivar *class_copyIvarList(Class cls, unsigned int *outCount)，获取类的成员变量列表的拷贝。调用后需要自己 free()。
Method class_getInstanceMethod(Class cls, SEL name)，获取指定名字的实例方法。
Method class_getClassMethod(Class cls, SEL name)，获取指定名字的类方法。
IMP class_getMethodImplementation(Class cls, SEL name)，获取指定名字的方法实现。
BOOL class_respondsToSelector(Class cls, SEL sel)，类是否响应指定的方法。
Method *class_copyMethodList(Class cls, unsigned int *outCount)，获取方法列表的拷贝。调用后需要自己 free()。
BOOL class_conformsToProtocol(Class cls, Protocol *protocol)，类是否遵循指定的协议。
Protocol * __unsafe_unretained *class_copyProtocolList(Class cls, unsigned int *outCount)，获取协议列表的拷贝。调用后需要自己 free()。
objc_property_t class_getProperty(Class cls, const char *name)，获取指定名字的属性。
objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount)，获取类的属性列表。调用后需要自己 free()。
BOOL class_addMethod(Class cls, SEL name, IMP imp, const char *types)，为类添加方法。
IMP class_replaceMethod(Class cls, SEL name, IMP imp, const char *types)，替代类的方法。
BOOL class_addIvar(Class cls, const char *name, size_t size, uint8_t alignment, const char *types)，给指定的类添加成员变量。这个函数只能在 objc_allocateClassPair() 和 objc_registerClassPair() 之间调用，并且不能为一个已经存在的类添加成员变量。
BOOL class_addProtocol(Class cls, Protocol *protocol)，为类添加协议。
BOOL class_addProperty(Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount)，为类添加属性。
void class_replaceProperty(Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount)，替代类的属性。
id class_createInstance(Class cls, size_t extraBytes)，创建指定类的实例。
id objc_constructInstance(Class cls, void *bytes)，在指定的位置创建类的实例。
void *objc_destructInstance(id obj)，销毁实例。
Class objc_allocateClassPair(Class superclass, const char *name, size_t extraBytes)，创建类和元类。
void objc_registerClassPair(Class cls)，注册类到 Runtime。
void objc_disposeClassPair(Class cls)，销毁类和对应的元类。
```

* 类是元类的实例。
* isa，在大多面向对象的语言中，都有类和对象的概念，其中，对象是类的实例，是通过类定义的结构生成出来的。而在 Objective-C 中，类本身也是一个对象，类作为对象时的 isa 指针指向的是元类(Meta Class)。
* object_getClass() 可以获得当前对象 isa

* `#pragma clang diagnostic...`代码，可用于忽略编译器对于未声明的 @selector 的 warning。因为我们的代码中我们需要动态的为一个类创建方法，所以必然不会事先声明。
```
#pragma clang diagnostic push
#pragma clang diagnostic ignored "-Wundeclared-selector"
//调用未声明的 @selector
#pragma clang diagnostic pop
```

* 使用 `objc_registerClassPair()` 函数需要注意，不能注册已经注册过的类。
* 使用 `objc_disposeClassPair()` 函数需要注意，如果一个类的实例和子类还存在时，不要去销毁一个类。

## 运行时的成员变量与属性
* 我们不能用 class_addIvar() 函数为一个已经存在的类添加 Ivar。并且 class_addIvar() 只能在 objc_allocateClassPair() 和 objc_registerClassPair() 之间调用。
* 添加一个属性及对应的成员变量后，我们还能通过 [obj valueForKey:@"propertyName"]; 获得属性值。

## 关联对象
可用于在类别中添加属性，创建一个category，然后添加property，并且实现setter,gertter。
```
- (void)setName:(NSString *)name{
objc_setAssociatedObject(self, "name", name, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (NSString *)name{
return objc_getAssociatedObject(self, "name");
}
```

相关方法：
```
void objc_setAssociatedObject(id object, const void *key, id value, objc _AssociationPolicy policy)
id objc_getAssociatedObject(id object, const void *key)
void objc_removeAssociatedObjects(id object)
```

## 方法与消息
* 需要`#import <objc/message.h>`
1. IMP 其实就是 implementation 的缩写，表示方法实现的代码块地址
> IMP定义：`id (*IMP)(id, SEL,...)`

2.SEL用于唯一标识一个方法名,因此一个类中是不能存在两个同名的方法的，即使参数类型不同也不行。
获取SEL的三种方法：
(1)sel_registerName函数
(2)Objective-C编译器提供的@selector()
(3)NSSelectorFromString()方法

3.Method
将[receiver message]转化为一个消息函数的调用
> 定义：`objc_msgSend(receiver, selector, arg1, arg2,...)`

## 消息机制
> 注意：使用objc_msgSend，需要`#import <objc/runtime.h>`
```
// 通过类名获取类
Class catClass = objc_getClass("Cat"); 

//注意Class实际上也是对象，所以同样能够接受消息，向Class发送alloc消息
Cat *cat = objc_msgSend(catClass, @selector(alloc)); 

//发送init消息给Cat实例cat
cat = objc_msgSend(cat, @selector(init)); 

//发送eat消息给cat，即调用eat方法
objc_msgSend(cat, @selector(eat));

//汇总消息传递过程
objc_msgSend(objc_msgSend(objc_msgSend(objc_getClass("Cat"), sel_registerName("alloc")), sel_registerName("init")), sel_registerName("eat"));
```

* objc_msgSend()报错`Too many arguments to function call ,expected 0,have3`
> Build Setting--> Apple LLVM 6.0 - Preprocessing--> Enable Strict Checking of objc_msgSend Calls  改为 NO

## 方法交换
```
+ (void)load{
Method eatMethod = class_getInstanceMethod(self, @selector(eat));
Method playMethod = class_getInstanceMethod(self, @selector(play));
method_exchangeImplementations(eatMethod, playMethod);
}
```

## 动态加载方法
```
void run(id self, SEL _cmd, NSNumber *number){
NSLog(@"%@", number);
}

//收到run:消息时候，为该类添加一个方法实现
+ (BOOL)resolveInstanceMethod:(SEL)sel{
if(sel == NSSelectorFromString(@"run:")){
class_addMethod(self, @selector(run:), run, "v@:@");
return YES;
}
return [super resolveInstanceMethod:sel];
}
//另外针对类方法的为 resolveClassMethod
```

## 消息转发
Method Swizzling（俗称黑魔法）就是在运行时将一个方法的实现代替为另一个方法的实现。Runtime提供三种方式来将原来的方法实现代替掉，那该怎样选择它们呢？
1.Method Resolution：由于Method Resolution不能像消息转发那样可以交给其他对象来处理，所以只适用于在原来的类中代替掉。
2.Fast Forwarding：它可以将消息处理转发给其他对象，使用范围更广，不只是限于原来的对象。
3.Normal Forwarding：它跟Fast Forwarding一样可以消息转发，但它能通过NSInvocation对象获取更多消息发送的信息，例如：target、selector、arguments和返回值等信息。

消息转发机制基本上分为三个步骤：
第一步：动态方法解析。
第二步：备用接收者。
第三步：完整转发。
```
//  第一步，消息接收者没有找到对应的方法时候，会先调用此方法，可在此方法实现中动态添加新的方法
//  返回YES表示相应selector的实现已经被找到，或者添加新方法到了类中，否则返回NO
+ (BOOL)resolveInstanceMethod:(SEL)sel {
return YES;
}

//  第二步， 如果第一步的返回NO或者直接返回了YES而没有添加方法，该方法被调用
//  在这个方法中，我们可以指定一个可以返回一个可以响应该方法的对象， 注意如果返回self就会死循环
- (id)forwardingTargetForSelector:(SEL)aSelector {
//    if ([NSStringFromSelector(aSelector) isEqualToString:@"run:"]) {
//        return [[RuntimeMethodHelper alloc] init];
//    }
//    return [super forwardingTargetForSelector:aSelector];
return nil;
}

//  第三步， 如果forwardingTargetForSelector:返回了nil，则该方法会被调用，系统会询问我们要一个合法的『类型编码(Type Encoding)』
//  函数的最后一个参数 types 是描述方法返回值和参数列表的字符串，我们的代码中的用到的 i@:@ 四个字符分别对应着：返回值 int32_t、参数 id self、参数 SEL _cmd、参数 NSDictionary *dic,即类型编码。
//  若返回 nil，则不会进入下一步，而是无法处理消息
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector {
    NSMethodSignature *signature = [super methodSignatureForSelector:aSelector];
    if (!signature) {
        if ([NSStringFromSelector(aSelector) isEqualToString:@"run:"]){
            return [NSMethodSignature signatureWithObjCTypes:"v@:"];
        }
        return [super methodSignatureForSelector: aSelector];
    }
    return signature;
}

// 当实现了此方法后，-doesNotRecognizeSelector: 将不会被调用
// 在这里进行消息转发
- (void)forwardInvocation:(NSInvocation *)anInvocation {

if ([RuntimeMethodHelper instancesRespondToSelector:anInvocation.selector]) {
[anInvocation invokeWithTarget:[[RuntimeMethodHelper alloc] init]];
}

//    // 在这里可以改变方法选择器
//    [anInvocation setSelector:@selector(run:)];
//    // 改变方法选择器后，需要指定消息的接收者
//    [anInvocation invokeWithTarget:self];
}

//- (void)run:(NSNumber *)num {
//    NSLog(@"---%@", num); // Print: <RuntimeMethodHelper: 0x7f814b498ee0>, 0x102d79929
//}

// 如果没有实现消息转发 forwardInvocation  则调用此方法
- (void)doesNotRecognizeSelector:(SEL)aSelector {
NSLog(@"unresolved method ：%@", NSStringFromSelector(aSelector));
}
```

### 深入理解黑魔法
当项目规模比较大，各个控制器功能都已经实现，突然增加每个页面都需要修改的需求，实现方式有以下几种：
1.每个页面进行修改
2.继承
3.category
4.Method Swizzling
Method Swizzling本质上就是对IMP和SEL进行交换。Method Swizzling也是iOS中AOP(面相切面编程)的一种实现方式。
在OC语言的runtime特性中，调用一个对象的方法就是给这个对象发送消息。是通过查找接收消息对象的方法列表，从方法列表中查找对应的SEL，这个SEL对应着一个IMP(一个IMP可以对应多个SEL)，通过这个IMP找到对应的方法调用。
在每个类中都有一个Dispatch Table，这个Dispatch Table本质是将类中的SEL和IMP(可以理解为函数指针)进行对应。而我们的Method Swizzling就是对这个table进行了操作，让SEL对应另一个IMP。
在实现Method Swizzling时，核心代码主要就是一个runtime的C语言API：
```
OBJC_EXPORT void method_exchangeImplementations(Method m1, Method m2) 
__OSX_AVAILABLE_STARTING(__MAC_10_5, __IPHONE_2_0);
```

## 字典转模型应用
```
+ (instancetype)modelWithDict:(NSDictionary *)dict{
    id model = [[self alloc] init];
    unsigned int count = 0;

    Ivar *ivars = class_copyIvarList(self, &count);
    for (int i = 0 ; i < count; i++) {
        Ivar ivar = ivars[i];

        NSString *ivarName = [NSString stringWithUTF8String:ivar_getName(ivar)];

        //这里注意，拿到的成员变量名为_cid,_age
        ivarName = [ivarName substringFromIndex:1];
        id value = dict[ivarName];

        [model setValue:value forKeyPath:ivarName];
    }

    return model;
}
```

[demo传送门](https://github.com/guchunli/RuntimeDemo)

# runloop运行循环
* 保持程序的持续运行。
* 处理App中的各类事件（触摸事件、定时器事件、Selector事件）
* 节省CPU资源，提高程序性能：没有事件时就进行睡眠状态

## 内部实现
do-while循环，在这个循环内部不断地处理各种任务（Source\Timeer\Observer）,Source和Timer这两个是主动向RunLoop发送消息的，Observer是被动接收消息的。CDRunLoopRunSpecific具体处理runloop的运行情况。

## runloop与线程
### 一个线程对应一个RunLoop（采用字典存储，线程号为key，RunLoop为value），RunLoop在第一次获取时创建，在线程结束时销毁
### 主线程的RunLoop默认已经启动，子线程的RunLoop需要手动启动
### RunLoop只能选择一个Mode启动，如果当前Mode没有任何Source、Timer、Observer，那么就不会进入RunLoop

* runloop生命周期：第一次获取时创建（currentRunloop），每条线程对应唯一的runloop，线程结束runloop销毁
* 主runloop：在applicationMain中自动创建runloop，主runloop与主线程相关，主线程中runloop自动启动运行，子线程中需要手动创建
* 获取runloop：mainRunloop,currentRunloop（懒加载）
```
//    [[NSRunLoop currentRunLoop] addTimer:timer forMode:NSDefaultRunLoopMode];
NSRunLoop *currentRunloop = [NSRunLoop currentRunLoop];
[NSTimer scheduledTimerWithTimeInterval:1.f target:self selector:@selector(run) userInfo:nil repeats:YES];
[currentRunloop run];
```

## iOS中访问和使用RunLoop的API
* Foundation--NSRunLoop
* Core Foundation--CFRunLoopRef(开源)
因为后者是开源的，且前者是在后者上针对OC的封装，所以一般是对CFRunLoopRef进行研究。

### 两套API对应获取RunLoop对象的方式：
> 值得注意的是，获取当前RunLoop都是进行懒加载的，也就是调用时自动创建线程对应的RunLoop。
* Foundation
```
[NSRunLoop currentRunLoop]; // 当前runloop
[NSRunLoop mainRunLoop];// 主线程runloop
```

* Core Foundation
```
CFRunLoopGetCurrent();// 当前runloop
CFRunLoopGetMain();// 主线程runloop
```

NSRunLoop转化为CFRunLoop：runloop.getCFRunloop

### 相关类
CFRunLoopRef,mode,source（set）,timer（array）,observer（array），每个runloop中至少要有一个source或timer

## runloop的运行模式
* CFRunLoopModeRef代表RunLoop的运行模式，一个RunLoop可以包含多个Mode，每个Mode可以包含多个Source、Timer、Observer。
* 每次RunLoop启动时，只能指定其中一个Mode，这个Mode就变成了CurrentMode
* 当启动RunLoop时，如果所在Mode中没有Source、Timer、Observer，那么将不会进入RunLoop，会直接结束
* 如果要切换Mode，只能退出Loop，再重新制定一个Mode进入

### 系统默认注册了5个Mode
* NSDefaultRunLoopMode：App的默认Mode，通常主线程是在这个Mode下运行
* NSRunLoopCommonModes: 这是一个占位用的Mode，不是一种真正的Mode，mode集合
* UITrackingRunLoopMode：界面跟踪 Mode，追踪触摸滑动，保证界面滑动时不受其他 Mode 影响
* UIInitializationRunLoopMode: 在刚启动 App 时第进入的第一个 Mode，启动完成后就不再使用
* GSEventReceiveRunLoopMode: 接受系统事件的内部 Mode，通常用不到

### NSRunLoopCommonModes
* 一个Mode可以将自己标记为“Common”属性，每当 RunLoop 的内容发生变化时，RunLoop会对标记有“Common”属性的Mode进行相适应的切换，并同步Source/Observer/Timer
* 在主线程中，kCFRunLoopDefaultMode 和 UITrackingRunLoopMode这两个Mode都是被默认标记为“Common”属性的，从输出的主线程RunLoop可以查看。

### source（timer,input）
* 根据函数调用栈分为：
source0：不基于port，用户主动触发的
source1：基于Port，系统的

### observer
* 监听runloop的状态改变
```
/*
第一个参数：分配空间
第一个参数：监听哪个状态
第一个参数：是否持续监听
第一个参数：优先级
第一个参数：回调
*/
CFRunLoopObserverRef observer = CFRunLoopObserverCreateWithHandler(CFAllocatorGetDefault(), kCFRunLoopAfterWaiting, YES, 0, ^(CFRunLoopObserverRef observer, CFRunLoopActivity activity) {

/*
kCFRunLoopEntry = (1UL << 0),
kCFRunLoopBeforeTimers = (1UL << 1),
kCFRunLoopBeforeSources = (1UL << 2),
kCFRunLoopBeforeWaiting = (1UL << 5),
kCFRunLoopAfterWaiting = (1UL << 6),
kCFRunLoopExit = (1UL << 7),
kCFRunLoopAllActivities = 0x0FFFFFFFU
*/
switch (activity) {
case kCFRunLoopAfterWaiting:
NSLog(@"即将进入休眠");
break;

default:
break;
}
});
```

### runloop的应用
1.timer
2.imageView的显示
3.performSelector
4.常驻线程：线程完成任务自动挂掉，如果在完成任务后保持runloop，则不会挂掉。
```
[[NSRunLoop currentRunLoop]addPort:[NSPort port] forMode:NSDefaultRunLoopMode];
```
5.自动释放池
什么时候创建：启动runloop
最后一次销毁：退出runloop
其他时候的创建和销毁：runloop即将进入休眠时会销毁之前的，重新创建一个新的

参考：[iOS 模块详解—「Runtime面试、工作」](https://www.jianshu.com/p/19f280afcb24)
[Objective-C 的 Runtime](http://www.samirchen.com/objective-c-runtime/)
[iOS Runtime 几种基本用法简记](http://www.jianshu.com/p/99af00237cb8)
[iOS运行时(Runtime)详解+Demo](http://www.jianshu.com/p/adf0d566c887)
[https://www.jianshu.com/p/ed65518ec8db](https://www.jianshu.com/p/ed65518ec8db)

[iOS runtime和runloop](https://www.jianshu.com/p/ebc6e20b84cf)
[深入理解RunLoop](https://blog.ibireme.com/2015/05/18/runloop/#more-41710)
