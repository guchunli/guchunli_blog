---
title: OC中的block
date: 2017-07-01 14:59:03
categories: 笔记
tags: [OC,block]
---

## 1.block定义：returnType(^blockName)(parameterTypes) = ^(parameters) {};
## 2.block用copy修饰，delegate用weak修饰
## 3.MRC环境下：
### (1)block引用局部变量
局部变量a:block代码块中使用局部变量,会自动拷贝一份到常量区,所以不可改变量
如果要修改局部变量，需要加__block修饰变量
<!--more-->
```
__block int number = 10;
void (^myBlock)(void) = ^(void) {
number = 20;    //不用__block，则不能修改
NSLog(@"%d",number);;
};

```

### (2)block中引用一个局部OC对象
该对象会被retain，如果局部变量使用__block修饰，则不会retain
```
__block NSObject *obj = [[NSObject alloc]init];
void (^myBlock)(void) = ^(void) {
NSLog(@"%ld",obj.retainCount);  //不用__block，则为2
};
myBlock();
```

### (3)block中引用一个全局变量
在block代码块中使用全局变量或方法时,会将这个变量或方法所在的对象self引用计数加1,引起循环引用
解决方法:使用__block修饰self
```
__block SecondViewController *weakSelf =self;
```

## 4.ARC环境下：
### (1)在block中引用局部变量，同MRC需要__block修饰
### (2)在block中引用局部对象，不用加__block
### (3)在block中引用全局变量，
```
_index = 1;
__weak SecondViewController *weakThis = self;
[btn addTapAction:^(UIButton *btn) {

//可修改值,但控制器不销毁,发生了循环引用
//        _index = 2;
//        NSLog(@"index:%ld",_index);

//属性
__strong SecondViewController *strongThis = weakThis;
strongThis->_index = 2;
NSLog(@"index:%ld",strongThis->_index);

//方法
//[weakThis test];
[strongThis test];
}];

```

## 5.__weak与__block区别
MRC，__block 修饰，可以避免循环引用；ARC，__block 修饰，同样会引起循环引用问题；
__block不管是ARC还是MRC模式下都可以使用，可以修饰对象，还可以修饰基本数据类型；
__weak只能在ARC模式下使用，也只能修饰对象，不能修饰基本数据类型；
(1)block下循环引用的问题
__block本身并不能避免循环引用，避免循环引用需要在block内部把__block修饰的obj置为nil
__weak可以避免循环引用，但是其会导致外部对象释放了之后，block 内部也访问不到这个对象的问题，我们可以通过在 block 内部声明一个 __strong
的变量来指向 weakObj，使外部对象既能在 block 内部保持住，又能避免循环引用的问题
(2)__block与__weak功能上的区别。
__block会持有该对象，即使超出了该对象的作用域，该对象还是会存在的，直到block对象从堆上销毁；而__weak仅仅是将该对象赋值给weak对象，当该对象销毁时，weak对象将指向nil；
__block可以让block修改局部变量，而__weak不能。
另外，MRC中__block是不会引起retain；但在ARC中__block则会引起retain。所以ARC中应该使用__weak。


