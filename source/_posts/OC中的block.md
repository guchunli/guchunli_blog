---
title: OC中的block
date: 2017-07-01 14:59:03
categories: 笔记
tags: [block]
---

## 定义/底层
* 实质：C语言的匿名函数，提前准备一段代码，在需要的时候调用。
* 底层：是一个指针结构体，是一个OC NSObject对象，它内部也有个isa指针，block是封装了函数调用以及函数调用环境的OC对象。

## block的使用
* 用作本地变量
```
returnType (^blockName)(parameterTypes) = ^returnType(parameters) {...};
```
<!--more-->
* 用作属性
`block用copy/strong修饰，delegate用weak修饰，枚举用assign修饰`
```
@property (nonatomic, copy, nullability) returnType (^blockName)(parameterTypes);
As a method parameter:
```

* 用作方法参数
```
- (void)someMethodThatTakesABlock:(returnType (^nullability)(parameterTypes))blockName;
```

* 调用参数为block的方法
```
[someObject someMethodThatTakesABlock:^returnType (parameters) {...}];
```

* 用作类型
```
typedef returnType (^TypeName)(parameterTypes);
TypeName blockName = ^returnType(parameters) {...};
```

## 存储位置
block块的存储位置（block块入口地址）：可能存放在2个地方：代码区（NSConcreteGlobalBlock）、堆区（NSConcreteMallocBlock），程序分5个区，还有常量区、全局区和栈区，对于MRC情况下代码还可能存在栈区(NSConcreteStackBlock)。
* 情况1：代码区
不访问处于栈区的变量（例如局部变量），且不访问处于堆区的变量(例如alloc创建的对象)。也就是说可以访问全局变量、静态变量。
```
/**
没有访问任何变量
*/
int main(int argc, char * argv[]) {
void (^block)(void) = ^{
NSLog(@"===");
};
block();
}
/**
访问了全局（静态）变量
*/
int  iVar = 10;
int main(int argc, char * argv[]) {
void (^block)(void) = ^{
NSLog(@"===%d",iVar);
};
block();
}
```

* 情况2：堆区
如果访问了处于栈区的变量（例如局部变量），或处于堆区的变量(例如alloc创建的对象)，都会存放在堆区。(实际是放在栈区，然后ARC情况下自动又拷贝到堆区)
* block也是属于“函数”的范畴，即一段代码。为什么要将其放在堆区呢，而不是直接在代码区呢？
如果不放到堆区，而放在代码区，那么block捕获的self对象将永远不会释放，因为代码区的block是不会释放的，那内存的泄露可就随处可见了。所以苹果这么做也是有原因的。
```
/**
访问局部变量
*/
int main(int argc, char * argv[]) {
int iVar = 10;
void (^block)(void) = ^{
NSLog(@"===%d",iVar);
};
block();
}
```

## 捕获变量
block里面捕获的变量，都是副本。
```
int val = 10;
void (^block)(void) = ^{
NSLog(@"val = %d",val); // 10
};
val = 5;
block();
```

1：可以捕获不可以修改变量
* 局部变量（局部变量auto：值传递）
为什么不可以修改：局部变量在执行Block语法的时候，被block捕获成为Block的结构体实例中，但是Block仅仅捕获了val的值，并没有捕获val的内存地址，所以在Block内部是无法修改自动变量的值。block代码块中使用局部变量,会自动拷贝一份到常量区,所以不可改变量。我们在定义的时候就已经将val作为参数传递进去了。也就是在定义的时候我们的block就获取到了val的值，而且不管后面怎么修改val的值。我们在block内部获取的val都是定义的时候传进来的值。
```
int val = 10;
void (^block)(void) = ^{
NSLog(@"val = %d",val);
// val = 1; //不允许
};
val = 5;
block();
```

2：可以捕获且可以修改变量
* 全局变量
为什么可以修改：因为是全局的，作用域很广，所以Block捕获了它们进去之后，在Block里面进行++操作，Block结束之后，它们的值依旧可以得以保存下来。
```
// 定义全局变量
int val = 10; 

void (^block)(void) = ^{
val = 1;
NSLog(@"val = %d",val); // 1
};
val = 5;
block();
```

* 静态变量（局部变量static：指针传递）
为什么可以修改：静态变量传递给Block是内存地址值，所以能在Block里面直接改变值。在执行Block语法的时候，Block语法表达式所使用的静态变量的地址是被保存进了Block的结构体实例中，也就是Block自身中。所以能够在Block 内部修改静态变量的值。
```
static int val = 10; // 定义静态变量
void (^block)(void) = ^{
val = 1;
NSLog(@"val = %d",val); // 1
};
val = 5;
block();
```

* __block修饰的局部变量
    * __block可以用于解决block内部无法修改auto变量值的问题
    * __block不能修饰全局变量、静态变量（static）
    * 编译器会将__block变量包装成一个对象
使用__block来修饰val，之后val会被拷贝到堆上，之后无论是在block里面还是在val之前所处的作用域，访问的都是出于堆区的val。
为什么非要__block呢，因为如果不用__block，如果出了val所在的“}”,那么val就会被释放，而block的调用时机是不定的，可能调用时机已经超出了block和val本身所处的"{}"，再访问val就可能坏地址访问（val已经被释放）。所以这样做是合理的。
但是在block里面，类似self.name = xxx,self->_val，却是很常见的，self也没有用__block修饰呀？
self.name = xxx——>[self setName:xxx];是发送消息，函数调用。
self->_val，_val本身是处于堆区的。
```
__block int val = 10;
void (^block)(void) = ^{
val = 1; 
NSLog(@"val = %d",val);  // 1
};
val = 5;
block();
```

总结：在Block中改变变量值有3种方式，一是改变全局变量，二是传递内存地址指针到Block中（静态变量，属性），三是改变存储区方式(__block)。
             
## Block 分类
OC中，一般Block就分为以下3种，_NSConcreteGlobalBlock，_NSConcreteStackBlock，_NSConcreteMallocBlock。都继承NSBlock类型。
* _NSConcreteGlobalBlock（__NSGlobalBlock__）：
没有用到外界变量或只用到全局变量、静态变量的block（没有访问auto变量）
生命周期：从创建到应用程序结束。
调用copy后：副本源存储在程序的数据区域，copy不做任何操作
* _NSConcreteStackBlock(__NSStackBlock__)：
只用到外部局部变量、成员属性变量，且没有强指针引用的block都是StackBlock。（访问了auto变量）
生命周期：由系统控制的，一旦返回之后，就被系统销毁了。
调用copy后：副本源存储在栈区，copy从栈复制到堆
* _NSConcreteMallocBlock(__NSMallocBlock__)：
有强指针引用或copy修饰的成员属性引用的block会被复制一份到堆中成为MallocBlock，没有强指针引用即销毁（调用了copy）
生命周期：由程序员控制
调用copy后：副本源存储在堆区，copy引用计数增加

* 指定为copy后是浅拷贝还是深拷贝？因为block是一段代码，即不可变的，对不可变对象进行copy操作是浅复制。

## Block的copy
在ARC环境下，编译器会根据情况自动将栈上的block复制到堆上，比如以下情况：
* 1.block作为函数返回值时
```
// 定义一个block
typedef void(^CSBlock)(void);

// 定义一个返回blcok的函数
CSBlock myBlock() {
int age = 10;
return ^{
NSLog(@"age = %d",age);
};
}

int main(int argc, const char * argv[]) {
@autoreleasepool {
// insert code here...

// 1.block作为函数返回值
CSBlock block = myBlock();
block();
NSLog(@"%@",[block class]);
}
return 0;
}
```

* 2.将block赋值给__strong指针时
```
// 定义一个block
typedef void(^CSBlock)(void);

int main(int argc, const char * argv[]) {
@autoreleasepool {
// 2.block有被强指针引用
// ARC - 环境下 block - __NSMallocBlock__
// MRC - 环境下 block - __NSStackBlock__
int age = 10;
CSBlock block = ^{
NSLog(@"---------%d", age);
};
NSLog(@"%@", [block class]);
}
return 0;
}
```

* 3.block作为Cocoa API中方法名含有usingBlock的方法参数时
```
// 3.block作为Cocoa API中方法名含有usingBlock的方法参数时
NSArray *array = [NSArray array];
[array enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {

}];
```
* 4.block作为GCD API的方法参数时
```
// 4.block作为GCD API的方法参数时
dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{            
});
```

block属性写法：
* MRC下block属性的建议写法
@property (copy, nonatomic) void (^block)(void);
* ARC下block属性的建议写法
@property (strong, nonatomic) void (^block)(void);
@property (copy, nonatomic) void (^block)(void);

## 注意
### 判断block是否为空
block代码存在代码区，代码存放在堆区时，就需要特别注意，因为堆区不像代码区不变化，堆区是不断变化的（不断创建销毁）。因此代码有可能会被销毁（当没有强指针指向时），如果这时再访问此段代码则会程序崩溃。因此，对于这种情况，在定义一个block属性时应指定为strong或copy。我们在使用时最好判断下block是否为空。
```
- (void)blockTest {
    // 如果为空则返回
    if (!block) {
    NSLog(@"block is nil");
    return;
    }
    block();
}
```

## 循环引用问题
```
@interface BlockViewController ()
@property (nonatomic, strong) void (^block)(void);
@property (nonatomic, copy) NSString *str;
@end

@implementation BlockViewController

- (void)viewDidLoad {
[super viewDidLoad];
//self.block强引用block，而block中又使用了self.str，所以block强引用self，造成强引用
self.block = ^{
self.str = @"123";
};
}
@end
```

解决循环引用问题：
```
@interface BlockViewController ()
@property (nonatomic, strong) void (^block)(void);
@property (nonatomic, copy) NSString *str;
@end

@implementation BlockViewController

- (void)viewDidLoad {
[super viewDidLoad];
__weak __typeof(self) weakSelf = self;
self.block = ^{
weakSelf.str = @"123";
};
}
@end
```

## __weak与__block区别
* 本质：
__block会持有该对象，即使超出了该对象的作用域，该对象还是会存在的，直到block对象从堆上销毁；
__weak仅仅是将该对象赋值给weak对象，当该对象销毁时，weak对象将指向nil；

1._block不管是ARC还是MRC模式下都可以使用，可以修饰对象，还可以修饰基本数据类型。
2._weak只能在ARC模式下使用，也只能修饰对象（NSString），不能修饰基本数据类型（int）。
3._block对象可以在block中被重新赋值，_weak不可以。

* block下循环引用的问题
MRC，__block 修饰，可以避免循环引用；ARC，__block 修饰，同样会引起循环引用问题；
__block本身并不能避免循环引用，避免循环引用需要在block内部把__block修饰的obj置为nil
__weak可以避免循环引用，但是其会导致外部对象释放了之后，block 内部也访问不到这个对象的问题，我们可以通过在 block 内部声明一个 __strong
的变量来指向 weakObj，使外部对象既能在 block 内部保持住，又能避免循环引用的问题
MRC中__block是不会引起retain；但在ARC中__block则会引起retain。所以ARC中应该使用__weak。

### 当不再使用指向block的指针时，将其置空
当有类对象的成员变量pBlock指向block时，一方面是调用方，调用pBlock调用完成后，应将pBlock置为nil;另一方面是被调用方即block函数内部使用到self时要__weak声明。其实__weak声明有很多注意事项，下面是一个经典例子（正确的写法）：
```
__weak __typeof(self) weakSelf = self;
self.observer = [[NSNotificationCenter defaultCenter] addObserverForName:@"blockTest" object:nil queue:nil usingBlock:^(NSNotification * _Nonnull note) {
// 多线程情况下（假设发出通知的代码在另一线程下），有可能在usingBlock调用时，执行if (!strongSelf.block)时strongSelf还没有释放，而执行到strongSelf.block()的时候strongSelf已在其它线程被释放，造成调用失败
__strong __typeof(self) strongSelf = weakSelf;
//if (strongSelf == nil) {
//    return;
//}
// 下面再对strongSelf进行访问
// 防止block为空
if (!strongSelf.block) {
return;
}
strongSelf.block();
// 如果不用应置空，养成好习惯
strongSelf.block = nil;
NSLog(@"%@",strongSelf);
}];
```

* weakSelf
弱声明：防止block强引用self，造成循环引用：通知中心持有self.observer，observer又强引用 usingBlock，usingBlock又强引用self，self就不会被释放，那么dealloc就不会被调用(即使在dealloc中写了[[NSNotificationCenter defaultCenter] removeObserver:self.observer]也不会调用，因为dealloc没有被调用)，就造成内存泄露；

* strongSelf
strong强引用：为了在函数生命周期中防止self提前释放。在多线程情况下（假设发出通知的代码在另一线程下），有可能在usingBlock调用时，执行if (!strongSelf.block)时strongSelf还没有释放，而执行到strongSelf.block()的时候strongSelf已在其它线程被释放，造成调用失败（最大的问题是不统一，造成不可预知的错误。用__strong操作后保证要么都访问成功，要么都访问失败或者判断为空后直接return退出）。
如果执行usingBlock时self已经被释放则后面的strongSelf均为nil，因为对weakSelf引用计数为0再retain一次也不会有变化；
如果执行usingBlock时self没有释放，strongSelf是一个局部变量，当block执行完毕就会释放自动变量strongSelf，不会对self进行一直进行强引用。


参考：[iOS中block的使用、实现底层、循环引用、存储位置](https://www.cnblogs.com/mddblog/p/4754190.html)

