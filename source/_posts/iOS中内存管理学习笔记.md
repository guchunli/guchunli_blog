---
title: iOS中内存管理学习笔记
date: 2018-06-25 16:20:20
categories: 笔记
tags: [内存管理,MRC,ARC]
toc:
---

## MRC
答：
| 对象操作 | Objective-C方法 |    对应的操作结果  |
|:--------:|:------:|----------:|
| 生成并持有对象  |  alloc, new, copy,mutableCopy等方法  |  生成对象并设置引用计数 =1 |
| 持有对象  |  reatain方法  |  使引用计数 +1 |
| 释放对象  |  release方法  |  使引用计数 -1 |
| 废弃对象  |  dealloc方法  |  引用计数 =0 时调用（系统自动调用） |

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
当一个autorelease pool(自动释放池)被drain(销毁)的时候会对pool里的对象发送一条release的消息.
* 在ARC项目中我们同样可以创建NSAutoreleasePool类对象去帮助我们更精确的管理内存问题。
* NSAutoreleasePool的管理范围是在NSAutoreleasePool *pool = [[NSAutoreleasePool alloc]init];与[pool release];之间的对象
* 既然ARC项目中设置了ARC，为什么还要使用@autoreleasepool?（注意a的案例解释）ARC 并不是舍弃了@autoreleasepool，而是在编译阶段帮你插入必要的 retain/release/autorelease的代码调用。所以，跟你想象的不一样，ARC 之下依然是延时释放的，依然是依赖于 NSAutoreleasePool，跟非 ARC模式下手动调用那些函数本质上毫无差别，只是编译器来做会保证引用计数的正确性
* NSAutoreleasePool *pool=[[NSAutoreleasePool alloc] init]; 当执行[pool autorelease]的时候，系统会进行一次内存释放，把autorelease的对象释放掉，如果没有NSAutoreleasePool, 那这些内存不会释放
注意，对象并不是自动被加入到当前pool中，而是需要对对象发送autorelease消息，这样，对象就被加到当前pool的管理里了。当当前pool接受到drain消息时，它就简单的对它所管理的所有对象发送release消息。
* 在ARC项目中.不能直接使用autorelease pools,而是使用@autoreleasepool{},@autoreleasepool{}比直接使用NSAutoreleasePool效率高。不使用ARC的时候也可以使用(autorelease嵌套）

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

## 属性修饰关键字

### 写一个setter方法用于完成@property(nonatomic,retain)NSString *name;写一个setter方法用于完成@property(nonatomic, copy)NSString *name;
<!--more-->
答: 
retain表示持有特性，setter方法将传入参数先保留，再赋值，传入参数的retaincount会+1;
copy表示拷贝特性，setter方法将传入对象复制一份；需要完全一份新的变量时。
```
-(void)setName:(NSString *)str{
[str retain];
[name release];
name = str;
}

- (void)setName:(NSString *)str{
id t = [str copy];
[name release];
name = t;  
}
```


参考文档：[iOS中 property中的属性strong 、weak、copy 、assign 、retain 、unsafe_unretained 与autoreleasing区别和作用详解](https://blog.csdn.net/queenlysun/article/details/52681858)
[block使用时的一些情况以及防止循环引用](https://blog.csdn.net/cyj_sky/article/details/51442732)

