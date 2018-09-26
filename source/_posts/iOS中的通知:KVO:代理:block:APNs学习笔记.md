---
title: iOS中的通知/KVO/代理/block/APNs学习笔记
date: 2018-06-21 12:17:14
categories: 笔记
tags: [通知,KVO,代理,block]
toc:
---

## 通知/KVO/代理
三者优缺点比较
### delegate
* 优势 ：
1.非常严格的语法。所有监听到的事件必须是在delegate协议中有清晰的定义。
2.如果delegate中的一个方法没有实现那么就会出现编译警告/错误
<!--more-->
3.协议必须在controller的作用域范围内定义
4.在一个应用中的控制流程是可跟踪的并且是可识别的；
5.在一个控制器中可以定义多个不同的协议，每个协议有不同的delegate
6.没有第三方对象要求保持/监视通信过程。
7.能够接收调用的协议方法的返回值。这意味着delegate能够提供反馈信息给controller
8.经常被用在存在父子关系的对象之间通信，例如控制器和控制器的view（自己加的理解）
* 缺点
1.需要定义很多代码：1.协议定义；2.controller的delegate属性；3.在delegate本身中实现delegate方法定义
2.在释放代理对象时，需要小心的将delegate改为nil。一旦设定失败，那么调用释放对象的方法将会出现内存crash
3.在一个controller中有多个delegate对象，并且delegate是遵守同一个协议，但还是很难告诉多个对象同一个事件，不过有可能。
4.经常用在一对一的通信。（不知道是缺点还是优点，只能算是特点）（自己加的理解）

### notification
* 优势 ：
1.不需要编写多少代码，实现比较简单
2.对于一个发出的通知，多个对象能够做出反应，即一对多的方式实现简单
3.controller能够传递context对象（dictionary），context对象携带了关于发送通知的自定义的信息
* 缺点 ：
1.在编译期不会检查通知是否能够被观察者正确的处理； 
2.在释放注册的对象时，需要在通知中心取消注册；
3.在调试的时候应用的工作以及控制过程难跟踪；
4.需要第三方对象来管理controller与观察者对象之间的联系；
5.controller和观察者需要提前知道通知名称、UserInfo dictionary keys。如果这些没有在工作区间定义，那么会出现不同步的情况；
6.通知发出后，controller不能从观察者获得任何的反馈信息（相比较delegate）。

### KVO
* 优势
1.能够提供一种简单的方法实现两个对象间的同步。例如：model和view之间同步；
2.能够对非我们创建的对象，即内部对象的状态改变作出响应，而且不需要改变内部对象（SKD对象）的实现；
3.能够提供观察的属性的最新值以及先前值；
4.用key paths来观察属性，因此也可以观察嵌套对象；
5.完成了对观察对象的抽象，因为不需要额外的代码来允许观察值能够被观察
6.可以一对多。
* 缺点
1.我们观察的属性必须使用strings来定义。因此在编译器不会出现警告以及检查；
2.对属性重构将导致我们的观察代码不再可用；
3.复杂的“IF”语句要求对象正在观察多个值。这是因为所有的观察代码通过一个方法来指向；
4.当释放观察者时不需要移除观察者。

### delegate与NSNotification
通知：广播机制，可以将一个通知发送给多个监听者。随处都可以添加订阅
代理：一对一，每个对象的代理却只能有一个。用代理代码分布结构更加清晰
* 效率 肯定是delegate比NSNotification高。
delegate方法比notification更加直接，最典型的特征是，delegate方法往往需要关注返回值， 也就是delegate方法的结果。比如-windowShouldClose:，需要关心返回的是yes还是no。所以delegate方法往往包含 should这个很传神的词。也就是好比你做我的delegate，我会问你我想关闭窗口你愿意吗？你需要给我一个答案，我根据你的答案来决定如何做下一 步。相反的，notification最大的特色就是不关心接受者的态度， 我只管把通告放出来，你接受不接受就是你的事情，同时我也不关心结果。所以notification往往用did这个词汇，比如 NSWindowDidResizeNotification，那么NSWindow对象放出这个notification后就什么都不管了也不会等待接 受者的反应。

### KVO和NSNotification
和delegate一样，KVO和NSNotification的作用也是类与类之间的通信，与delegate不同的是
1）这两个都是负责发出通知，剩下的事情就不管了，所以没有返回值；
2）delegate只是一对一，而这两个可以一对多。这两者也有各自的特点。

## 通知
```
//接受消息的类
- (void)viewDidLoad {  
[super viewDidLoad];   
// 1.向通知中心添加的观察者(通知接受者)
// 2.观察者收到通知后进行的事件响应
// 3.通知的名字
// 4.接受固定对象的通知,当写成nil时,就是当前通知的消息发送者的通知都接收  
[[NSNotificationCenter defaultCenter]addObserver:self selector:@selector(notificationAction:) name:@"96.1FM" object:nil];}
//该参数就是发送过来的通知,接到通知后执行的方法
- (void)notificationAction:(NSNotification *)notify
{   
NSLog(@"%@111",notify.userInfo);
}
- (void)dealloc{ 
//移除观察者   
[[NSNotificationCenter defaultCenter]removeObserver:self name:@"96.1FM" object:nil];
}

//发送消息的类
- (void)viewDidLoad {  
[super viewDidLoad];      
//发送通知
//如果发送的通知指定了object对象，那么观察者接收的通知设置的object对象与其一样，才会接收到通知，但是接收通知如果将这个参数设置为了nil，则会接收一切通知。
[[NSNotificationCenter defaultCenter]postNotificationName:@"96.1FM" object:nil userInfo:@{@"name":@"123"}];    
}
```

### 多线程下的通知
NSNotificationCenter消息的接受线程是基于发送消息的线程的，也就是同步的。

## KVO相关

### KVO
* 使用场景：当需要检测其他类的属性值变化，但又不想被观察的类知道，就可以使用KVO了。
很适合实现 mode 模型和 view 视图之间的通讯。

* 触发
如果赋值没有通过 setter 方法或者 KVC，而是直接修改属性对应的成员变量，例如：仅调用 _name = @"newName"，这时是不会触发 KVO 机制，更加不会调用回调方法的。
所以使用 KVO 机制的前提是遵循 KVO 的属性设置方式来变更属性值。

* 底层实现原理
系统实现KVO有以下几个步骤：
1.当类A的对象第一次被观察的时候，系统会在运行期动态创建类A的派生类NSKVONotifying_A。该类继承自对象A的本类。
2.在派生类NSKVONotifying_A中重写类A的setter方法，NSKVONotifying_A类在被重写的setter方法中实现通知机制。setter 方法会负责在调用原 setter 方法之前和之后，通知所有观察对象属性值的更改情况。
3.类NSKVONotifying_A会重写 class方法，将自己伪装成类A。类NSKVONotifying_A还会重写dealloc方法释放资源。
4.系统将所有指向类A对象的isa指针指向类NSKVONotifying_A的对象。
    
KVC和KVO都属于键值编程而且底层实现机制都是isa-swizzing，依赖于Runtime机制。当观察者被注册为一个对象的属性的观察对象的isa指针被修改，指向一个中间类，而不是在真实的类。其结果是，isa指针的值并不一定反映实例的实际类。所以不能依靠isa指针来确定对象是否是一个类的成员。应该使用class方法来确定对象实例的类。
isa 指针的作用：每个对象都有 isa 指针，指向该对象的类，它告诉 Runtime 系统这个对象的类是什么。所以对象注册为观察者时，isa 指针指向新子类，那么这个被观察的对象就神奇地变成新子类的对象（或实例）了。

* KVO与线程
KVO的响应和KVO观察的值变化是在一个线程上的，所以，大多数时候，不要把KVO与多线程混合起来。除非能够保证所有的观察者都能线程安全的处理KVO。

* 手动KVO
首先，需要手动实现属性的 setter 方法，并在设置操作的前后分别调用 willChangeValueForKey: 和 didChangeValueForKey方法，这两个方法用于通知系统该 key 的属性值即将和已经变更了；
```
-(void)setName:(NSString *)newName{ 
    [self willChangeValueForKey:@"name"];    //KVO 在调用存取方法之前总调用 
    [super setValue:newName forKey:@"name"]; //调用父类的存取方法 
    [self didChangeValueForKey:@"name"];     //KVO 在调用存取方法之后总调用
}

-(void)setAge:(NSUInteger)age{  
    if (age < 22) {  
        return;  
    }  
    [self willChangeValueForKey:@age];  
    _age = age;  
    [self didChangeValueForKey:@age];
}
```

其次，要实现类方法 automaticallyNotifiesObserversForKey，并在其中设置对该 key 不自动发送通知（返回 NO 即可）。这里要注意，对其它非手动实现的 key，要转交给 super 来处理。
```
+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key{
    return NO;
}
```

### KVC
* 可以通过字符串的名字（key）来访问类属性的机制。而不是通过调用Setter、Getter方法访问。
* 可以访问私有成员变量的值，可以间接修改私有成员变量的值。可以修改readonly属性。
* 关键方法定义在 NSKeyValueCodingProtocol
* KVC支持类对象和内建基本数据类型。

* 使用：
获取值
valueForKey: 传入NSString属性的名字。
valueForKeyPath: 属性的路径，xx.xx
valueForUndefinedKey 默认实现是抛出异常，可重写这个函数做错误处理

修改值
setValue:forKey:
setValue:forKeyPath:
setValue:forUnderfinedKey:
setNilValueForKey: 对非类对象属性设置nil时调用，默认抛出异常。

* 解析json
重写2个方法 和 一个处理类型的方法；
```
#pragma mark ---- 利用kvc解析json，一定要实现下面的两个方法！(属性名字用这个方法)
-(void)setValue:(id)value forUndefinedKey:(NSString *)key{
    //找到和属性不一致名字的key，然后赋值给self的属性
    if ([key isEqualToString:@"description"]) {
        // self.descriptionStr = value; // 不推荐使用
        [self setValue:value forKey:@"descriptionStr"]; // 推荐
    }
}

-(id)valueForUndefinedKey:(NSString *)key{
    return nil;
}

#pragma mark --- 对于处理“类型”，就用下面的方法
// 处理特殊 ----（类型）例如：NSNumber--> NSString
- (void)setValue:(id)value forKey:(NSString *)key
{
    // price 服务器是 NSNumber
    // 服务器是 NSNumber ，模型表里是 NSString类型，所以，要处理
    if ([value isKindOfClass:[NSNumber class]]) {
        // NSNumber--> NSString
        [self setValue:[NSString stringWithFormat:@"%@",value] forKey:key];
    }else{
        [super setValue:value forKey:key];
    }
}
```

#### 底层实现原理
KVC运用了isa-swizzing技术。isa-swizzing就是类型混合指针机制。KVC通过isa-swizzing实现其内部查找定位。isa指针（is kind of 的意思）指向维护分发表的对象的类，该分发表实际上包含了指向实现类中的方法的指针和其他数据。
比如说如下的一行KVC代码：
```
[site setValue:@"sitename" forKey:@"name"];
//会被编译器处理成
SEL sel = sel_get_uid(setValue:forKey);
IMP method = objc_msg_loopup(site->isa,sel);
method(site,sel,@"sitename",@"name");
```
每个类都有一张方法表，是一个hash表，值是函数指针IMP，SEL的名称就是查表时所用的键。
SEL数据类型：查找方法表时所用的键。定义成char*，实质上可以理解成int值。
IMP数据类型：他其实就是一个编译器内部实现时候的函数指针。当Objective-C编译器去处理实现一个方法的时候，就会指向一个IMP对象，这个对象是C语言表述的类型。

* KVC的内部机制：
一个对象在调用setValue的时候进行了如下操作：
1.根据方法名找到运行方法的时候需要的环境参数
2.他会从自己的isa指针结合环境参数，找到具体的方法实现接口。
3.再直接查找得来的具体的实现方法

* 苹果为什么要用子类(就是C语言创建的那个子类)监听setter方法，而不用分类(Person+AWKVO)呢？
回答：原因是当你用分类监听setter方法的时候，Person类中setter方法就不会走了，这样不好，所以苹果使用了子类监听setter方法。

### 比较objectForKey与objectForKeyPath
* 区别
1.如:sum/average/max/min
```
NSArray *array1 = @[@1, @3, @5, @7, @9,@11, @13];
NSInteger sum = [[array1 valueForKeyPath:@"@sum.floatValue"] integerValue];
NSInteger avg = [[array1 valueForKeyPath:@"@avg.floatValue"] integerValue];
NSInteger max = [[array1 valueForKeyPath:@"@max.floatValue"] integerValue];
NSInteger min = [[array1 valueForKeyPath:@"@min.floatValue"] integerValue];
NSLog(@"sum--%ld--avg--%ld-max--%ld-min--%ld",(long)sum,(long)avg,(long)max,(long)min);
//注意:此种写法将引起崩溃
//    NSInteger sum = [[array1 valueForKey:@"@sum.floatValue"] integerValue];
//    NSInteger avg = [[array1 valueForKey:@"@avg.floatValue"] integerValue];
//    NSInteger max = [[array1 valueForKey:@"@max.floatValue"] integerValue];
//    NSInteger min = [[array1 valueForKey:@"@min.floatValue"] integerValue];
//    NSLog(@"sum--%ld--avg--%ld-max--%ld-min--%ld",(long)sum,(long)avg,(long)max,(long)min);
```
2.删除数组中重复的数据
```
NSArray *array2 = @[@1, @3, @5, @7, @9,@11, @13, @7, @9,@11];
NSLog(@"deleteKeyPath---%@",[array2 valueForKeyPath:@"@distinctUnionOfObjects.self"]);
//下述写法不可取,会引起崩溃
//NSLog(@"deleteKey---%@",[array2 valueForKey:@"@distinctUnionOfObjects.self"]);
```

3.深层次取字典中出子属性
* valueForKeyPath:可以深层次取到子属性，不管隐藏的多深，不只是字典套字典，对象套对象/对象套对象再套字典等情况，都可以通过链式调用到深层的值
* valueForKey:无法取到深层次子属性
```
NSDictionary *dic = @{@"dic1":@{@"dic2":@{@"name":@"zhangsanfeng",@"info":@{@"age":@"13"}}}};
//可以深层次的取到子层级属性
NSLog(@"KeyPath---%@",[dic valueForKeyPath:@"dic1.dic2.info.age"]); //13
//无法深层次取到子层级属性
NSLog(@"Key---%@",[dic valueForKey:@"dic1.dic2.info.age"]); //null
```

* 相同
1.快速找到字典数组中key所对应的值
对于@[@{key:value},@{key:value},@{key:value}]的数组(数组元素是字典的),通过同一个key可以取到value的集合(数组)
```
NSArray *dicArray = @[
@{@"company":@"baidu",@"person":@{@"name":@"zhangsanfeng"}},
@{@"company":@"tencent",@"position":@"chengdu"}];
NSLog(@"keyPath---%@", [dicArray valueForKeyPath:@"company"]);
NSLog(@"key-- -%@", [dicArray valueForKey:@"company"]);

keyPath---(
baidu,
tencent
)
key-- -(
baidu,
tencent
)

```

2.大小写字母转换
```
NSArray *array3 = @[@"name",@"w",@"b",@"h",@"g",@"d",@"r",@"p"];
NSLog(@"KeyPath---%@",[array3 valueForKeyPath:@"uppercaseString"]);
NSArray *newArray = [array3 valueForKey:@"uppercaseString"];
NSLog(@"newArray---%@",newArray);
```

### 比较objectForKey与valueForKey
* valueforkey：是KVC的方法， Key只允许使用NSString类型。
* objectforkey：是NSDictionary的方法，Key可以是任意类型。
* 在NSDictionary中的差异：一般来说 key 可以是任意字符串组合，如果 key 不是以 @ 符号开头，这时候 valueForKey: 等同于 objectForKey:，如果是以 @ 开头，objectForKey 可以正确取值，但是 valueForKey 取值会直接 crash 掉，所以在使用NSDictionary取值时，尽量使用objectForKey。
```
NSDictionary *dict = [NSDictionary dictionaryWithObject:@"theValue" forKey:@"@theKey"];// 注意这个 key 是以 @ 开头
NSString *value1 = [dict objectForKey:@"@theKey"];
NSString *value2 = [dict valueForKey:@"@theKey"];
```
　　　　　

## APNs
### 远程推送
我们的设备和APNS服务器之间的通讯是基于SSL协议的TCP流通讯，二者之间维持一个长连接。

### 远程推送的实现原理
1.打开App时: 发送UDID和BundleID给APNs加密后返回deviceToken
2.获取Token后，App调用接口,将用户身份信息和deviceToken发给服务器，服务器记录
3.当推送消息时, 服务器按照用户身份信息找到存储的deviceToken，将消息和deviToken发送给APNs
4.苹果的APNs通过deviceToken, 找到指定设备的指定程序, 并将消息推送给用户

### Feedback service
APNS会持续的更新Feedback service的列表，当我们的Provider将信息发给APNS推送到我们的设备时，如果这时设备无法将消息推送到指定的应用，就会向APNS服务器 报告一个反馈信息，而这个信息就记录在feedback service中。

参考文章：
[iOS中如何选择delegate、通知、KVO（以及三者的区别）](https://www.cnblogs.com/wsnb/p/4899719.html)
[深入理解 KVC\KVO 实现机制](http://www.cnblogs.com/zy1987/p/4616764.html)
[iOS开发系列--通知与消息机制](https://www.cnblogs.com/kenshincui/p/4168532.html)
