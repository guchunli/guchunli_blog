---
title: OC中的内存管理机制与属性相关
copyright: true
date: 2018-12-13 18:36:38
categories: 笔记
tags: [面试]
toc:
---


## OC中的内存管理机制
MRC(manual retain-release)手动内存管理
ARC(automatic reference counting)自动引用计数： 在iOS5.0 (Xcode4) 版本后推出的。
<!--more-->

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

* NSString *name，非ARC下重写setter,getter方法
`一旦重写了getter,setter方法,必须使用@synthesize variable = _variable来区分属性名与方法名.`
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

* 为什么不能同时重写getter,setter？
用@property声明的成员属性,相当于自动生成了setter getter方法,如果重写了set和get方法,与@property声明的成员属性就不是一个成员属性了,是另外一个实例变量,而这个实例变量需要手动声明。所以会报错误。

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

@property (nonatomic, copy) NSString *string; 通过property_getAttributes(property)获取到attributes并打印出来之后的结果为：T@"NSString",C,N,V_string，T就代表类型，C就代表Copy，N代表nonatomic，V就代表对应的实例变量
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
unsafe_unretained：与weak类似，引用计数为0时，不会置nil，__unsafe_unretained 比 __weak 快。当明确知道对象的生命期时，选择 __unsafe_unretained 会有一些性能提升
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
