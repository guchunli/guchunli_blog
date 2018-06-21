---
title: iOS富文本的使用
date: 2017-12-18 16:38:52
categories: 笔记
tags: [富文本]
toc:
---

给文本添加富文本
* 设置富文本富文本：addAttributes
* 富文本拼接：appendAttributedString
<!--more-->
1.阴影
```
NSMutableAttributedString *text = [NSMutableAttributedString new];
NSMutableAttributedString *one = [[NSMutableAttributedString alloc] initWithString:@"Shadow"];
NSShadow *shadow = [[NSShadow alloc]init];
shadow.shadowBlurRadius = 5;
shadow.shadowOffset = CGSizeMake(0, 1);
shadow.shadowColor = [UIColor colorWithWhite:0.000 alpha:0.490];
[one addAttributes:@{NSShadowAttributeName:shadow,NSForegroundColorAttributeName:[UIColor whiteColor],NSFontAttributeName:[UIFont boldSystemFontOfSize:30]} range:NSMakeRange(0, 6)];
[text appendAttributedString:one];
label.attributedText = text;
```

2.空心字
```
[one addAttributes:@{NSStrokeColorAttributeName:[UIColor redColor],NSStrokeWidthAttributeName:@1.5} range:NSMakeRange(0, 6)];
```

3.特殊效果
```
[one addAttributes:@{NSTextEffectAttributeName:NSTextEffectLetterpressStyle,} range:NSMakeRange(0, 6)];
```

4.下划线、中划线
* 下划线：NSUnderlineColorAttributeName
```
[one addAttributes:@{NSUnderlineStyleAttributeName:@(NSUnderlineStyleSingle),NSUnderlineColorAttributeName:[UIColor redColor]];
```

* 中划线
```
[one addAttributes:@{NSStrikethroughStyleAttributeName:@(NSUnderlinePatternSolid|NSUnderlineStyleSingle),NSStrikethroughColorAttributeName:[UIColor greenColor]} range:NSMakeRange(0, 6)];
```

线样式：
```
typedef NS_ENUM(NSInteger, NSUnderlineStyle) {
NSUnderlineStyleNone                                    = 0x00,
NSUnderlineStyleSingle                                  = 0x01,
NSUnderlineStyleThick NS_ENUM_AVAILABLE(10_0, 7_0)      = 0x02,
NSUnderlineStyleDouble NS_ENUM_AVAILABLE(10_0, 7_0)     = 0x09,

NSUnderlinePatternSolid NS_ENUM_AVAILABLE(10_0, 7_0)      = 0x0000,
NSUnderlinePatternDot NS_ENUM_AVAILABLE(10_0, 7_0)        = 0x0100,
NSUnderlinePatternDash NS_ENUM_AVAILABLE(10_0, 7_0)       = 0x0200,
NSUnderlinePatternDashDot NS_ENUM_AVAILABLE(10_0, 7_0)    = 0x0300,
NSUnderlinePatternDashDotDot NS_ENUM_AVAILABLE(10_0, 7_0) = 0x0400,

NSUnderlineByWord NS_ENUM_AVAILABLE(10_0, 7_0)            = 0x8000
} NS_ENUM_AVAILABLE(10_0, 6_0);
```

5.链接
* label上的链接点击不了，textView可以，回调函数`shouldInteractWithURL`中可以响应。
```
[one addAttributes:@{NSLinkAttributeName:@"https://www.baidu.com"} range:NSMakeRange(0, 6)];
```

6.段落
```
NSMutableParagraphStyle *paragraph = [[NSMutableParagraphStyle alloc]init];
paragraph.lineSpacing = 10.f;     //行间距
paragraph.firstLineHeadIndent = 30.f;   //段落首行缩进
paragraph.paragraphSpacing = 30.f;    //段落间距
paragraph.headIndent = 15.f;    //整体缩进
[one addAttributes:@{NSParagraphStyleAttributeName:paragraph}
```

7.图文混排
```
NSMutableAttributedString *one = [[NSMutableAttributedString alloc] initWithString:@"红红火火恍恍惚惚"];
NSTextAttachment *attachment = [[NSTextAttachment alloc]init];
attachment.image = [UIImage imageNamed:@"star_selected"];
NSAttributedString *imgAttr = [NSAttributedString attributedStringWithAttachment:attachment];
[one insertAttributedString:imgAttr atIndex:4];
```



