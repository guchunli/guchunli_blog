---
title: iOS单元测试学习笔记
date: 2017-04-09 14:16:58
categories: 笔记
tags: [测试]
---

1.新建项目时注意勾选`Unit Tests`,`UI Tests`
如果忘记勾选，File-->new-->target-->iOS-->Cocoa Unit Testing Bundle/Cocoa UI Testing Bundle 添加
<!--more-->
2.setUp是每个测试方法调用前执行，tearDown是每个测试方法调用后执行
3.测试方法必须testXXX的格式，且不能有参数
4.测试方法的执行顺序是字典序排序,cmd + U进行单元测试，这个快捷键是全部测试，在代码左侧就是平常打断点的地方，有个菱形按钮是单独测试这个方法
5.iOS9的http安全问题：现在进行异步请求的网络测试，由于测试方法主线程执行完就会结束，所以需要设置一下，否则没法查看异步返回结果。在方法结束前设置等待，调回回来的时候再让它继续执行。
```
//waitForExpectationsWithTimeout是等待时间，超过了就不再等待往下执行。
#define WAIT do {\
[self expectationForNotification:@"RSBaseTest" object:nil handler:nil];\
[self waitForExpectationsWithTimeout:30 handler:nil];\
} while (0);

#define NOTIFY \
[[NSNotificationCenter defaultCenter]postNotificationName:@"RSBaseTest" object:nil];
```

## 常用的断言测试函数
XCTFail(format…)  //生成一个失败的测试；
XCTAssertNil(a1, format...)  //为空判断，a1为空时通过，反之不通过；
XCTAssertNotNil(a1, format…) //不为空判断，a1不为空时通过，反之不通过；
XCTAssert(expression, format...) //当expression求值为TRUE时通过；
XCTAssertTrue(expression, format...) //当expression求值为TRUE时通过；
XCTAssertFalse(expression, format...)  //当expression求值为False时通过；
XCTAssertEqualObjects(a1, a2, format...)  //判断相等，[a1 isEqual:a2]值为TRUE时通过，其中一个不为空时，不通过；
XCTAssertNotEqualObjects(a1, a2, format...)  //判断不等，[a1 isEqual:a2]值为False时通过；
XCTAssertEqual(a1, a2, format...)  //判断相等（当a1和a2是 C语言标量、结构体或联合体时使用,实际测试发现NSString也可以）；
XCTAssertNotEqual(a1, a2, format...)  //判断不等（当a1和a2是 C语言标量、结构体或联合体时使用）；
XCTAssertEqualWithAccuracy(a1, a2, accuracy, format...)  //判断相等，（double或float类型）提供一个误差范围，当在误差范围（+/-accuracy）以内相等时通过测试；
XCTAssertNotEqualWithAccuracy(a1, a2, accuracy, format...)   //判断不等，（double或float类型）提供一个误差范围，当在误差范围以内不等时通过测试；
XCTAssertThrows(expression, format...)  //异常测试，当expression发生异常时通过；反之不通过；
XCTAssertThrowsSpecific(expression, specificException, format...)  //异常测试，当expression发生specificException异常时通过；反之发生其他异常或不发生异常均不通过；
XCTAssertThrowsSpecificNamed(expression, specificException, exception_name, format...)  //异常测试，当expression发生具体异常、具体异常名称的异常时通过测试，反之不通过；
XCTAssertNoThrow(expression, format…)  //异常测试，当expression没有发生异常时通过测试；
XCTAssertNoThrowSpecific(expression, specificException, format...)  //异常测试，当expression没有发生具体异常、具体异常名称的异常时通过测试，反之不通过；
XCTAssertNoThrowSpecificNamed(expression, specificException, exception_name, format...)  //异常测试，当expression没有发生具体异常、具体异常名称的异常时通过测试，反之不通过

示例代码：
```
// XCTFail(format…)  //生成一个失败的测试；
//        XCTFail(@"Fail");

// XCTAssertNil(a1, format...) 为空判断， a1 为空时通过，反之不通过；
//        XCTAssertNil(@"not nil string", @"string must be nil");

// XCTAssertNotNil(a1, format…) 不为空判断，a1不为空时通过，反之不通过；
XCTAssertNotNil(@"not nil string", @"string can not be nil");


// XCTAssert(expression, format...) 当expression求值为TRUE时通过；
//        XCTAssert((2 > 2), @"expression must be true");

// XCTAssertTrue(expression, format...) 当expression求值为TRUE时通过；
XCTAssertTrue(1, @"Can not be zero");

// XCTAssertFalse(expression, format...) 当expression求值为False时通过；
XCTAssertFalse((2 < 2), @"expression must be false");

// XCTAssertEqualObjects(a1, a2, format...) 判断相等， [a1 isEqual:a2] 值为TRUE时通过，其中一个不为空时，不通过；
//    XCTAssertEqualObjects(@"1", @"1", @"[a1 isEqual:a2] should return YES");
//    XCTAssertEqualObjects(@"1", @"2", @"[a1 isEqual:a2] should return YES");

//     XCTAssertNotEqualObjects(a1, a2, format...) 判断不等， [a1 isEqual:a2] 值为False时通过，
//    XCTAssertNotEqualObjects(@"1", @"1", @"[a1 isEqual:a2] should return NO");
//    XCTAssertNotEqualObjects(@"1", @"2", @"[a1 isEqual:a2] should return NO");

// XCTAssertEqual(a1, a2, format...) 判断相等（当a1和a2是 C语言标量、结构体或联合体时使用,实际测试发现NSString也可以）；
// 1.比较基本数据类型变量
//    XCTAssertEqual(1, 2, @"a1 = a2 shoud be true"); // 无法通过测试
//    XCTAssertEqual(1, 1, @"a1 = a2 shoud be true"); // 通过测试

// 2.比较NSString对象
//    NSString *str1 = @"1";
//    NSString *str2 = @"1";
//    NSString *str3 = str1;
//    XCTAssertEqual(str1, str2, @"a1 and a2 should point to the same object"); // 通过测试
//    XCTAssertEqual(str1, str3, @"a1 and a2 should point to the same object"); // 通过测试

// 3.比较NSArray对象
//    NSArray *array1 = @[@1];
//    NSArray *array2 = @[@1];
//    NSArray *array3 = array1;
//    XCTAssertEqual(array1, array2, @"a1 and a2 should point to the same object"); // 无法通过测试
//    XCTAssertEqual(array1, array3, @"a1 and a2 should point to the same object"); // 通过测试

// XCTAssertNotEqual(a1, a2, format...) 判断不等（当a1和a2是 C语言标量、结构体或联合体时使用）；

// XCTAssertEqualWithAccuracy(a1, a2, accuracy, format...) 判断相等，（double或float类型）提供一个误差范围，当在误差范围（+/- accuracy ）以内相等时通过测试；
//    XCTAssertEqualWithAccuracy(1.0f, 1.5f, 0.25f, @"a1 = a2 in accuracy should return YES");

// XCTAssertNotEqualWithAccuracy(a1, a2, accuracy, format...) 判断不等，（double或float类型）提供一个误差范围，当在误差范围以内不等时通过测试；
//    XCTAssertNotEqualWithAccuracy(1.0f, 1.5f, 0.25f, @"a1 = a2 in accuracy should return NO");

// XCTAssertThrows(expression, format...) 异常测试，当expression发生异常时通过；反之不通过；（很变态）

// XCTAssertThrowsSpecific(expression, specificException, format...) 异常测试，当expression发生 specificException 异常时通过；反之发生其他异常或不发生异常均不通过；

// XCTAssertThrowsSpecificNamed(expression, specificException, exception_name, format...) 异常测试，当expression发生具体异常、具体异常名称的异常时通过测试，反之不通过；

// XCTAssertNoThrow(expression, format…) 异常测试，当expression没有发生异常时通过测试；

// XCTAssertNoThrowSpecific(expression, specificException, format...)异常测试，当expression没有发生具体异常、具体异常名称的异常时通过测试，反之不通过；

// XCTAssertNoThrowSpecificNamed(expression, specificException, exception_name, format...) 异常测试，当expression没有发生具体异常、具体异常名称的异常时通过测试，反之不通过
```

转自：[iOS单元测试(作用及入门提升)](http://www.jianshu.com/p/8bbec078cabe)
(http://www.jianshu.com/p/009844a0b9ed)
