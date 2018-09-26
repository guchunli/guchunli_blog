---
title: Xcode开发技巧总结
date: 2017-12-15 10:38:53
categories: 笔记
tags: [Xcode]
toc:
---

## 无线连接真机调试
1.用数据线将手机连到Xcode，`请确保手机和电脑连到同一个wifi`。
<!--more-->
2.Xcode：Window->Device and Simulators，找到连接的设备，将`Connect via network`打勾。
3.等待连接成功后，连接成功后设备那一列右边有一个地球标识，拔掉数据线，设备连接列表中选择真机即可进行无线调试。
4.还可以查看设备日志：Window->Device and Simulators->View Device Logs

* Passcode Required (“The device musthave a passcode set in order to allow this operation.”)
错误原因：可能手机未设置开机/锁屏密码导致，或者是开发工具Xcode的缓存所致。
解决方法：设置手机密码,然后再次点击Connect via network,可以正常连接,之后关掉手机密码后也可正常连接。

## Xcode控制台打印中文
### 1.宏定义
<!--more-->
```
#define NSLog(FORMAT, ...) fprintf(stderr,"\n %s:%d   %s\n",[[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String],__LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]);
```

### 2.添加类目，重写description方法：拼接
```
#import <Foundation/Foundation.h>

@implementation NSDictionary (Log)
- (NSString *)description
{
    NSMutableString *string = [NSMutableString string];

    // 开头有个{
    [string appendString:@"{\n"];

    // 遍历所有的键值对
    [self enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
        [string appendFormat:@"\t%@", key];
        [string appendString:@" : "];
        [string appendFormat:@"%@,\n", obj];
    }];

    // 结尾有个}
    [string appendString:@"}"];

    // 查找最后一个逗号
    NSRange range = [string rangeOfString:@"," options:NSBackwardsSearch];
    if (range.location != NSNotFound)
    [string deleteCharactersInRange:range];

    return string;
}
@end

@implementation NSArray (Log)

- (NSString *)description
{
    NSMutableString *string = [NSMutableString string];

    // 开头有个[
    [string appendString:@"[\n"];

    // 遍历所有的元素
    [self enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        [string appendFormat:@"\t%@,\n", obj];
    }];

    // 结尾有个]
    [string appendString:@"]"];

    // 查找最后一个逗号
    NSRange range = [string rangeOfString:@"," options:NSBackwardsSearch];
    if (range.location != NSNotFound)
    [string deleteCharactersInRange:range];

    return string;
}

@end
```

### 3.添加类目，重写description方法：转换成json格式字符串
```
#import <Foundation/Foundation.h>

@implementation NSDictionary (Log)
- (NSString *)description
{
    NSData *jsonData = [NSJSONSerialization dataWithJSONObject:self options:NSJSONWritingPrettyPrinted error:nil];
    NSString *jsonStr = [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];

    return jsonStr;
}
@end

@implementation NSArray (Log)

- (NSString *)description
{
    NSData *jsonData = [NSJSONSerialization dataWithJSONObject:self options:NSJSONWritingPrettyPrinted error:nil];
    NSString *jsonStr = [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
    
    return jsonStr;
}

@end
```
