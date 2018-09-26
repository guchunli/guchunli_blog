---
title: iOS生成图形验证码
date: 2018-02-07 14:04:18
categories: 笔记
tags: 
toc:
---

1.创建一个图形验证码视图
<!--more-->
```
#import <UIKit/UIKit.h>

@interface CodeView : UIView

@property (nonatomic,strong) NSArray           *changeArray;
@property (nonatomic,strong) NSMutableString   *changeString;
@property (nonatomic,strong) UILabel           *codeLabel;

-(void)changeCode;

@end
```

具体实现：
```
#import "CodeView.h"

@implementation CodeView

- (id)initWithFrame:(CGRect)frame
{
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code

        float red = arc4random() % 100 / 100.0;
        float green = arc4random() % 100 / 100.0;
        float blue = arc4random() % 100 / 100.0;
        UIColor *color = [UIColor colorWithRed:red green:green blue:blue alpha:0.2];
        self.backgroundColor = color;
        [self change];
    }
    return self;
}

-(void)changeCode
{
    [self change];
}

- (void)change
{
    //实际可以从后台请求数据
    NSInteger arcNum = arc4random_uniform(10000);
    self.changeString = [[NSMutableString alloc]initWithString:[NSString stringWithFormat:@"%ld",arcNum]];
    NSLog(@"%@",self.changeString);
    [self setNeedsDisplay];

}

- (void)drawRect:(CGRect)rect
{
    [super drawRect:rect];

    if (self.changeString.length > 0) {
        float red = arc4random() % 100 / 100.0;
        float green = arc4random() % 100 / 100.0;
        float blue = arc4random() % 100 / 100.0;

        UIColor *color = [UIColor colorWithRed:red green:green blue:blue alpha:0.5];
        [self setBackgroundColor:color];

        NSString *text = [NSString stringWithFormat:@"%@",self.changeString];
        CGSize cSize = [@"S" sizeWithAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:20]}];
        int width = rect.size.width / text.length - cSize.width;
        int height = rect.size.height - cSize.height;
        CGPoint point;

        float pX, pY;
        for (int i = 0; i < text.length; i++)
        {
            pX = arc4random() % width + rect.size.width / text.length * i;
            pY = arc4random() % height;
            point = CGPointMake(pX, pY);
            unichar c = [text characterAtIndex:i];
            NSString *textC = [NSString stringWithFormat:@"%C", c];
            [textC drawAtPoint:point withAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:20]}];
        }

        CGContextRef context = UIGraphicsGetCurrentContext();
        CGContextSetLineWidth(context, 1.0);
        for(int cout = 0; cout < 10; cout++)
        {
            red = arc4random() % 100 / 100.0;
            green = arc4random() % 100 / 100.0;
            blue = arc4random() % 100 / 100.0;
            color = [UIColor colorWithRed:red green:green blue:blue alpha:0.2];
            CGContextSetStrokeColorWithColor(context, [color CGColor]);
            pX = arc4random() % (int)rect.size.width;
            pY = arc4random() % (int)rect.size.height;
            CGContextMoveToPoint(context, pX, pY);
            pX = arc4random() % (int)rect.size.width;
            pY = arc4random() % (int)rect.size.height;
            CGContextAddLineToPoint(context, pX, pY);
            CGContextStrokePath(context);
        }
    }
}
```

2.使用
```
//验证码view
self.codeView = [[TGCodeView alloc]init];
UITapGestureRecognizer *codeTapGes = [[UITapGestureRecognizer alloc]initWithTarget:self action:@selector(codeViewTapEvent)];
[self.codeView addGestureRecognizer:codeTapGes];
self.codeView.frame = CGRectMake(70, 100, 70, 30);
[self.view addSubview:self.codeView];
```

验证码视图的点击事件：
```
- (void)codeViewTapEvent {
    [self.codeView changeCode];
}
```
