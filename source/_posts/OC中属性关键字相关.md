---
title: OC中属性关键字相关
copyright: true
date: 2018-12-14 11:38:12
categories: 笔记
tags: [面试]
toc:
---


## @property
1.本质：@property 其实就是在编译阶段由编译器自动帮我们生成ivar成员变量，getter方法，setter方法。

3.NSString *name，非ARC下重写setter,getter方法
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

4. 写一个setter方法用于完成@property(nonatomic,retain)NSString *name;写一个setter方法用于完成@property(nonatomic, copy)NSString *name;
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

5.为什么不能同时重写getter,setter？
用@property声明的成员属性,相当于自动生成了setter getter方法,如果重写了set和get方法,与@property声明的成员属性就不是一个成员属性了，是另外一个实例变量，而这个实例变量需要手动声明。所以会报错误。

## 属性关键字
* 默认值：atomic，readwrite，assign/strong(基本数据类型／对象)
### atomic/nonatomic
* atomic和nonatomic用来决定编译器生成的getter和setter是否为原子操作。
1. atomic：系统生成的 getter/setter 会保证 get、set 操作的完整性，不受其他线程影响。getter 还是能得到一个完好无损的对象（可以保证数据的完整性），但这个对象在多线程的情况下是不能确定的。
也就是说：如果有多个线程同时调用setter的话，不会出现某一个线程执行完setter全部语句之前，另一个线程开始执行setter情况，相当于函数头尾加了锁一样，每次只能有一个线程调用对象的setter方法，所以可以保证数据的完整性。
总结：atomic所说的线程安全只是保证了getter和setter存取方法的线程安全，并不能保证整个对象是线程安全的。
2. nonatomic：就没有这个保证了，nonatomic返回你的对象可能就不是完整的value。因此，在多线程的环境下原子操作是非常必要的，否则有可能会引起错误的结果。但仅仅使用atomic并不会使得对象线程安全，我们还要为对象线程添加lock来确保线程的安全。
* nonatomic的速度要比atomic的快
* atomic与nonatomic的本质区别其实也就是在setter方法上的操作不同。
nonatomic对象setter和getter方法的实现:
```
- (void)setCurrentImage:(UIImage *)currentImage
{
    if (_currentImage != currentImage) {
        [_currentImage release];
        _currentImage = [currentImage retain];
    }
}
- (UIImage *)currentImage{
    return _currentImage;
}
```
atomic对象setter和getter方法的实现:
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

- (UIImage *)currentImage{
    @synchronized(self) {
        return _currentImage;
    }
}
```

### 内存管理语义
assign：不更改引用计数，一般用于CCRect之类的结构体以及基本数据类型，代码实现是直接赋值
retain：一般用于OC对象类型用于MRC模式，代码实现是 
```
if(_temp != temp) { 
    [_temp release]; 
    _temp = [temp retain]
};  
return _temp;
```
copy：建立一个引用计数为1的对象，与 strong 类似，然而在设置新属性值时并不保留新值，而是拷贝一份，一般用于NSString类型以及Block，代码实现是 
```
if(_temp != temp) { 
    [_temp release]; 
    _temp = [temp copy]
};  
return _temp;
```
strong：一般用于OC对象用于ARC模式，代表用强指针指向对象
weak：一般用于OC对象用于ARC模式，代表用弱指针指向对象，对象消失后，指针自动置nil
unsafe_unretained：与weak类似，引用计数为0时，不会置nil，__unsafe_unretained 比 __weak 快。当明确知道对象的生命期时，选择 __unsafe_unretained 会有一些性能提升

### copy
copy/mutableCopy:用copy从一个可变对象拷贝出一个不可变对象时, 这种情况就属于深拷贝而不是浅拷贝.
浅拷贝/深拷贝:默认状态下深拷贝指的是不完全深拷贝, 如要实现完全深拷贝, 则要重写copyWithZone: 方法
block:copy.block在创建的时候它的内存是默认是分配在栈(stack)上, 而不是堆(heap)上的. 所以它的作用域仅限创建时候的当前上下文(函数, 方法...), 当你在该作用域外调用该block时, 程序就会崩溃.一般情况下你不需要自行调用copy或者retain一个block. 只有当你需要在block定义域以外的地方使用时才需要copy. Copy将block从内存栈区移到堆区.
其实block使用copy是MRC留下来的也算是一个传统吧, 在MRC下, 如上述, 在方法中的block创建在栈区, 使用copy就能把他放到堆区, 这样在作用域外调用该block程序就不会崩溃. 但在ARC下, 使用copy与strong其实都一样, 因为block的retain就是用copy来实现的
NSArray:    
```
NSMutableArray* dataSour = [NSMutableArray arrayWithObjects:@"90",@"80",@"100",nil];
// 获取数据，进行展示。
self.rankArray = dataSour;
// 正常显示排行。
NSLog(@"%@",self.rankArray);
[dataSour addObject:@"25"];
//  说好的不可变数组呢？ 为什么？突然冒出来了个 25？
NSLog(@"%@",self.rankArray); // 90,80,100,25
```
1.使用场景
* NSString、NSArray、NSDictionary 等等经常使用copy关键字，是因为他们有对应的可变类型：因为父类指针可以指向子类对象,使用 copy 的目的是为了让本对象的属性不受外界影响,使用 copy 无论给我传入是一个可变对象还是不可对象,我本身持有的就是一个不可变的副本.
* 用于block：编译器会自动对 block 进行了 copy 操作。使用 copy 是从 MRC 遗留下来的“传统”，在 MRC 中,方法内部的 block 是在栈区的,使用 copy 可以把它放到堆区.在 ARC 中写不写都行:对于 block 使用 copy 还是 strong 效果是一样的,但是建议写上 copy,因为这样显示告知调用者“编译器会自动对 block 进行了 copy 操作。
因为block默认申请的是栈内存，当block所在的方法执行完之后内存空间就被释放。为了可以在外部使用block，需要把block放入堆内存中。MRC中只有copy做到这一点，ARC中copy可以。

* 为什么NSString要用copy，而不用用strong？  (block  字典   数组同理)
一个场景：如果用NSString指向一个NSMutableString的内存空间的话，用strong修饰。当NSMutableString中内容改变时，NSString指针指向的也会改变，而用copy不会
必须使用copy的场景：A对象持有string记做A.string，然后赋值给B对象，记做B.string，若希望B.string的内容改变时A.string不改变就必须用copy
必须用strong的场景：若希望B.string的内容改变时同时A.string也改变则必须用strong

2.浅拷贝/深拷贝
区别：浅拷贝就是只拷贝对象，但是属性不拷贝，拷贝出来的对象和原来的对象共用属性，即指向同一个属性地址。
浅拷贝：指只是将对象内存地址多了一个引用，也就是说，拷贝结束之后，两个对象的值不仅相同，而且对象所指的内存地址都是一样的。
深拷贝：指拷贝一个对象的具体内容，拷贝结束之后，两个对象的值虽然是相同的，但是指向的内存地址是不同的。两个对象之间也互不影响，互不干扰。
深拷贝 : 拷贝出来的对象与源对象地址不一致! 这意味着我修改拷贝对象的值对源对象的值没有任何影响.
浅拷贝 : 拷贝出来的对象与源对象地址一致! 这意味着我修改拷贝对象的值会直接影响到源对象.
非集合类对象的copy和mutableCopy：
* 对不可变对象进行copy操作，只仅仅是指针复制（浅拷贝），进行mutableCopy操作，是内容复制（深拷贝）。
* 对可变对象进行copy和mutableCopy操作，都是内容复制（深拷贝）。
```
NSString *string = @"abc";
NSString *stringCopy = [string copy];
NSMutableString *stringMCopy = [string mutableCopy];
NSLog(@"string: %p, %p", string, &string);
//浅拷贝：新对象的内存地址并没有发生变化，改变的仅仅是指针的地址
NSLog(@"stringCopy: %p, %p", stringCopy, &stringCopy);
//深拷贝：内存地址已经发生了变化，并且指针地址发生变化
NSLog(@"stringMCopy: %p, %p", stringMCopy, &stringMCopy);

NSMutableString *string = [NSMutableString stringWithFormat:@"abc"];
NSString *stringCopy = [string copy];
NSMutableString *stringMCopy = [string mutableCopy];
NSLog(@"string: %p, %p", string, &string);
//深拷贝：内存地址和指针地址都发生了变化
NSLog(@"stringCopy: %p, %p", stringCopy, &stringCopy);
//深拷贝：内存地址和指针地址都发生了变化
NSLog(@"stringMCopy: %p, %p", stringMCopy, &stringMCopy);
```

集合类对象的copy和mutableCopy：
* 对于不可变的集合类对象进行copy操作，只是改变了指针，其内存地址并没有发生变化；进行mutableCopy操作，内存地址发生了变化，但是其中的元素内存地址并没有发生变化。
* 对于可变集合类对象，不管是进行copy操作还是mutableCopy操作，其内存地址都发生了变化，但是其中的元素内存地址都没有发生变化，属于单层深拷贝。
```
NSString *element_01 = @"abc";
NSString *element_02 = @"def";
NSString *element_03 = @"ghi";
NSArray *array = @[element_01, element_02, element_03];
NSArray *arrayCopy = [array copy];
NSMutableArray *arrayMCopy = [array mutableCopy];
NSLog(@"array: %p, %p; array.firstObject: %p", array, &array, array.firstObject);
//浅拷贝：只是改变了指针，其内存地址并没有发生变化，但是其中的元素内存地址并没有发生变化。
NSLog(@"arrayCopy: %p, %p; arrayCopy.firstObject: %p", arrayCopy, &arrayCopy, arrayCopy.firstObject);
//单层深拷贝：内存地址和指针地址都发生了变化，但是其中的元素内存地址并没有发生变化。
NSLog(@"arrayMCopy: %p, %p; arrayMCopy.firstObject: %p", arrayMCopy, &arrayMCopy, arrayMCopy.firstObject);

NSString *element_01 = @"abc";
NSString *element_02 = @"def";
NSString *element_03 = @"ghi";
NSMutableArray *array = [NSMutableArray arrayWithArray:@[element_01, element_02, element_03]];
NSArray *arrayCopy = [array copy];
NSMutableArray *arrayMCopy = [array mutableCopy];
NSLog(@"array: %p, %p; array.firstObject: %p", array, &array, array.firstObject);
//单层深拷贝：内存地址和指针地址都发生了变化，但是其中的元素内存地址并没有发生变化。
NSLog(@"arrayCopy: %p, %p; arrayCopy.firstObject: %p", arrayCopy, &arrayCopy, arrayCopy.firstObject);
//单层深拷贝：内存地址和指针地址都发生了变化，但是其中的元素内存地址并没有发生变化。
NSLog(@"arrayMCopy: %p, %p; arrayMCopy.firstObject: %p", arrayMCopy, &arrayMCopy, arrayMCopy.firstObject);
```

如何实现完全复制?
```
NSArray *copyArray = [[NSArray alloc] initWithArray:array copyItems:YES];  // 完全复制
```

3.为什么我们声明NSString, NSArray或者NSDictionary的时候，经常使用copy关键字，使用strong有什么区别？可变对象使用copy会有什么问题？
```
@property (nonatomic, strong) NSString *strStrong;
@property (nonatomic, copy) NSString *strCopy;

NSMutableString *string = [NSMutableString stringWithFormat:@"abc"];
self.strStrong = string;
self.strCopy = string;
NSLog(@"旧strStrong: %@", self.strStrong);//abc
NSLog(@"旧strCopy: %@", self.strCopy);//abc

[string appendFormat:@"def"];
NSLog(@"新strStrong: %@", self.strStrong);//abcdef
NSLog(@"新strCopy: %@", self.strCopy);//abc
```

注意： `@property (copy) NSMutableArray *array;`会有什么问题：
1) 因为copy是复制出一个不可变的对象，在不可变对象上运行可变对象的方法，添加,删除,修改数组内的元素的时候，程序会因为找不到对应的方法而崩溃
2) 使用了 atomic 属性会严重影响性能 。

4.自定义对象实现copy：
`copy`需要实现`NSCopying`协议，然后实现`NSCopying`协议方法，否则copy会crash；
`mutableCopy`时，需要实现`NSMutableCopying`协议，否则mutableCopy会crash。
```
@interface CopyModel : NSObject

@property (nonatomic, copy) NSString *title;
@property (nonatomic, copy) NSString *name;

@end

@implementation CopyModel

- (instancetype)copyWithZone:(NSZone *)zone {
CopyModel *copyModel = [[CopyModel allocWithZone:zone] init];
copyModel.title = self.title;
copyModel.name = self.name;
return copyModel;
}

@end
```

```
CopyModel *model = [[CopyModel alloc] init];
model.title = @"title";
model.name = @"name";

CopyModel *modelStrong = model;
CopyModel *modelCopy = [model copy];
NSLog(@"model: %p, %p", model, &model);
//其内存地址并没有发生变化，只是改变了指针
NSLog(@"modelStrong: %p, %p", modelStrong, &modelStrong);
//内存地址和指针地址都发生了变化
NSLog(@"modelCopy: %p, %p", modelCopy, &modelCopy);
```

### weak
weak是弱引用，用weak描述修饰或者所引用对象的计数器不会加一，并且会在引用的对象被释放的时候自动被设置为nil，大大避免了野指针访问坏内存引起崩溃的情况，另外weak还可以用于解决循环引用。
1.为什么IBOutlet修饰的UIView也适用weak关键字
UIViewController->UIView->subViews->UIButton
一般的IBOutlet直接关联到viewcontroller。但是跟其关联的控件并不是添加在controller上，而是添加到controller的view上，比如[self.view addSubView：xxx]; 这个时候self.view已经对xxx 强引用过了，self.view才是持有xxx的对象。这样子才符合引用计数的规则。所以直接IBOutlet顶级view的时候肯定是strong的。
如果将weak改为strong，也是没有问题的，并不会造成强引用循环。

2.runtime 如何实现 weak 属性
runtime 对注册的类， 会进行布局，对于 weak 对象会放入一个 hash 表中。hash 表用于存储指向某个对象的所有weak指针，Key是所指对象的地址，value是weak指针的地址（这个地址的值是所指对象指针的地址）数组。为什么value是数组？因为一个对象可能被多个弱引用指针指向。假如 weak 指向的对象内存地址是a，那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从而设置为 nil。

3.weak，__unsafe_unretained, unowned 与 assign区别
* __unsafe_unretained: 不会对对象进行retain,当对象销毁时,会依然指向之前的内存空间(野指针)
* weak: 不会对对象进行retain,当对象销毁时,会自动指向nil
* assign: 实质与__unsafe_unretained等同
* assigin 可以用非OC对象，而weak必须用于OC对象
* unsafe_unretained也可以修饰代表简单数据类型的property，weak不能修饰用来代表简单数据类型的property。
* __unsafe_unretained 与 weak 比较，使用 weak 是有代价的，因为__weak需要检查对象是否已经消亡，而为了知道是否已经消亡，自然也需要一些信息去跟踪对象的使用情况。也正因此，__unsafe_unretained 比 __weak快,所以当明确知道对象的生命期时，选择__unsafe_unretained 会有一些性能提升，这种性能提升是很微小的。但当很清楚的情况下，__unsafe_unretained 也是安全的，自然能快一点是一点。而当情况不确定的时候，应该优先选用 __weak 。
* unowned使用在Swift中，也会分 weak 和 unowned。unowned 的含义跟 __unsafe_unretained 差不多。假如很明确的知道对象的生命期，也可以选择 unowned。

### 循环引用
* 当A强引用B，B强引用A。这会让两个都有强指针指向结果都无法释放。解决办法把其中一个用weak申明
* 当block用copy属性修饰的时候，block会对其中用到的OC对象有强引用。解决办法：ARC把用到的oc对象用__weak声明，MRC用__block申明

### 什么情况会出现内存泄漏
* 当调用一些C语言的API用完没有关闭（如利用UIGraphicsBeginImageContextWithOptions打开了图形上下文，没有close），导致资源没有释放
* 出现循环引用的情况，导致资源没有释放
* MRC中将OC对象的释放权交给自动释放池，在池子被销毁前程序创建n个oc对象，将导致内存泄漏。

### 其他
* synthesize/dynamic
默认：@syntheszie var = _var;
@synthesize：编译期间，让编译器自动生成getter／setter方法，当有自定义的存或取方法时屏蔽自动生成的方法
@dynamic：告诉编译器:属性的 setter 与 getter 方法由用户自己实现,不自动生成。(当然对于 readonly 的属性只需提供 getter 即可)

假如一个属性被声明为 @dynamic var；然后你没有提供@setter 方法和@getter 方法,编译的时候没问题,但是当程序运行到 instance.var = someVar,由于缺 setter方法会导致程序崩溃;或者当运行到 someVar = instance.var 时,由于缺 getter 方法同样会导致崩溃。编译时没问题,运行时才执行相应的方法,这就是所谓的动态绑定。

* 方法名：getter=<name>、setter=<name>
```
//getter
@property (nonatomic, getter=isOn) BOOL on;
```

* 不常用：nonnull,null_resettable,nullable

