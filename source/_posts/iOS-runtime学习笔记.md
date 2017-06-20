---
title: iOS runtime学习笔记
date: 2017-03-22 11:39:18
categories: 笔记
tags: [iOS,runtime]
---

Objective-C 是一门动态语言，它把很多静态语言在编译和链接时做的事情放到了运行时去处理，它在运行时实现了对类、方法、成员变量、属性等信息的管理机制。

## 运行时的类与对象
<!--more-->
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

* `#pragma clang diagnostic...`代码，这是用于忽略编译器对于未声明的 @selector 的 warning。因为我们的代码中我们需要动态的为一个类创建方法，所以必然不会事先声明。
* `class_addMethod()` 函数的最后一个参数 types 是描述方法返回值和参数列表的字符串，我们的代码中的用到的 i@:@ 四个字符分别对应着：返回值 int32_t、参数 id self、参数 SEL _cmd、参数 NSDictionary *dic。这个其实就是类型编码(Type Encoding)的概念。在 Objective-C 中，为了协助 Runtime 系统，编译器会将每个方法的返回值和参数列表编码为一个字符串，这个字符串会与方法对应的 selector 关联。更详细的知识可以查阅 Type Encodings。
* 使用 `objc_registerClassPair()` 函数需要注意，你不能注册已经注册过的类。
* 使用 `objc_disposeClassPair()` 函数需要注意，如果一个类的实例和子类还存在时，不要去销毁一个类。

## 运行时的成员变量与属性
成员变量和属性相关的函数
```
Ivar class_getClassVariable(Class cls, const char *name)，返回指定类的指定名字的成员变量。
Ivar *class_copyIvarList(Class cls, unsigned int *outCount)，返回指定类的成员变量列表。调用后需要自己 free()。
BOOL class_addIvar(Class cls, const char *name, size_t size, uint8_t alignment, const char *types)，给指定的类添加成员变量。这个函数只能在 objc_allocateClassPair() 和 objc_registerClassPair() 之间调用，并且不能为一个已经存在的类添加成员变量。
id object_getIvar(id obj, Ivar ivar)，获得对象的指定成员变量的值。速度比 object_getInstanceVariable() 快。
void object_setIvar(id obj, Ivar ivar, id value)，设置对象指定成员变量的值。速度比 object_setInstanceVariable() 快。
Ivar object_getInstanceVariable(id obj, const char *name, void **outValue)，获取指定名字的成员变量的值。
Ivar object_setInstanceVariable(id obj, const char *name, void *value)，设置指定名字成员变量的值。
const char *ivar_getName(Ivar v)，获取成员变量名。
const char *ivar_getTypeEncoding(Ivar v)，获取成员变量的类型编码。
ptrdiff_t ivar_getOffset(Ivar v)，获取成员变量的偏移量。
objc_property_t class_getProperty(Class cls, const char *name), 获取指定类指定名字的属性。
objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount), 获取指定类的属性列表。调用后需要自己 free()。
BOOL class_addProperty(Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount)， 给指定的类添加属性。
void class_replaceProperty(Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount)，替代指定类的属性。
const char *property_getName(objc_property_t property)，获取属性名。
const char *property_getAttributes(objc_property_t property)，获取属性特性描述。
objc_property_attribute_t *property_copyAttributeList(objc_property_t property, unsigned int *outCount)，获取属性特性列表。调用后需要自己 free()。
char *property_copyAttributeValue(objc_property_t property, const char *attributeName)，获取属性特性值。调用后需要自己 free()。
```

* IMP 其实就是 implementation 的缩写，表示方法实现的代码块地址
* 我们不能用 class_addIvar() 函数为一个已经存在的类添加 Ivar。并且 class_addIvar() 只能在 objc_allocateClassPair() 和 objc_registerClassPair() 之间调用。
* 添加一个属性及对应的成员变量后，我们还能通过 [obj valueForKey:@"propertyName"]; 获得属性值。

## 运行时的消息分发
```id objc_msgSend(id self, SEL op, ...)，消息分发。(objc/message.h)
id method_invoke(id receiver, Method m, ...);，调用指定方法的实现。
void method_invoke_stret(id receiver, Method m, ...);，调用返回一个数据结构的方法的实现。
SEL method_getName(Method m);，获取方法名。
IMP method_getImplementation(Method m);，返回方法的实现。
const char * method_getTypeEncoding(Method m);，获取描述方法参数和返回值类型的字符串。
char * method_copyReturnType(Method m);，获取方法的返回值类型的字符串。
char * method_copyArgumentType(Method m, unsigned int index);，获取方法的指定位置参数的类型字符串。
void method_getReturnType(Method m, char *dst, size_t dst_len);，通过引用返回方法的返回值类型字符串。
unsigned int method_getNumberOfArguments(Method m);，返回方法的参数的个数。
void method_getArgumentType(Method m, unsigned int index, char *dst, size_t dst_len);，通过引用返回方法指定位置参数的类型字符串。
struct objc_method_description * method_getDescription(Method m);，返回指定方法的方法描述结构体。
IMP method_setImplementation(Method m, IMP imp);，设置方法的实现。注意该函数返回值是方法之前的实现。
void method_exchangeImplementations(Method m1, Method m2);，交换两个方法的实现。
const char * sel_getName(SEL sel);，返回给定选择器指定的方法的名称。
SEL sel_registerName(const char *str);，在Objective-C Runtime系统中注册一个方法，将方法名映射到一个选择器，并返回这个选择器。
SEL sel_getUid(const char *str);，在Objective-C Runtime系统中注册一个方法。
BOOL sel_isEqual(SEL lhs, SEL rhs);，比较两个选择器。
```

消息转发机制基本上分为三个步骤：
第一步：动态方法解析。
未知的方法被截获，程序不会崩溃
```
#import <objc/runtime.h>
- (void)viewDidLoad {
[super viewDidLoad];
[self performSelector:@selector(unknownMethod)];
}
void dealWithExceptionForUnknownMethod(id self, SEL _cmd) {
NSLog(@"%@, %p", self, _cmd); // Print: <ViewController: 0x7ff96be33e60>, 0x1078259fc
}
+ (BOOL)resolveInstanceMethod:(SEL)sel {
NSString *selectorString = NSStringFromSelector(sel);
if ([selectorString isEqualToString:@"unknownMethod"]) {
class_addMethod(self.class, @selector(unknownMethod), (IMP) dealWithExceptionForUnknownMethod, "v@:");
}
return [super resolveInstanceMethod:sel];
}
```

第二步：备用接收者。
如果在第一步还是无法处理消息，则 Runtime 会继续调以下方法：
```
- (id)forwardingTargetForSelector:(SEL)aSelector
```

这一步适用于当我们只想将消息转发到另一个能处理该消息的对象上的情况，它无法进一步对消息进行处理，比如：操作消息的参数和返回值。

第三步：完整转发。
如果第二步：备用接收者还是未能处理好消息，那么接下来只有启用完整的消息转发机制了，这时候会调用以下方法：
```
- (void)forwardInvocation:(NSInvocation *)anInvocation
```

运行时系统会在这一步给消息接收者最后一次机会将消息转发给其它对象。
另外还有一个重要的问题是我们必须重写下面方法：
```
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
```

如果不在以上所述的三个步骤中处理未知消息，到了 NSObject 那则会引发一个异常。

代码：[RuntimeDemo](https://github.com/guchunli/RuntimeDemo)

转自：[Objective-C 的 Runtime](http://www.samirchen.com/objective-c-runtime/)
