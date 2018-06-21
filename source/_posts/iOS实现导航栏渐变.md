---
title: iOS实现导航栏渐变
date: 2018-01-15 16:16:31
categories: 笔记
tags: [导航栏,渐变]
toc:
---

## 设置导航栏的barTintColor
1.获取状态栏加导航栏的总高度
<!--more-->
```
//状态条高度
#define TG_TANGO_STATUSBAR_HEIGHT [[UIApplication sharedApplication] statusBarFrame].size.height
//导航条高度
#define TG_TANGO_NAVBAR_HEIGHT 44.0
//导航条+状态条高度
#define TG_TANGO_SYSTEMTOP_HEIGHT (TG_TANGO_STATUSBAR_HEIGHT + TG_TANGO_NAVBAR_HEIGHT)
```

2.设置导航栏颜色
```
self.navigationController.navigationBar.barTintColor = [UIColor orangeColor];
```

3.滚动时修改导航栏透明度
```
- (void)scrollViewDidScroll:(UIScrollView *)scrollView{

    CGFloat offsetY = scrollView.contentOffset.y;

    if (offsetY <= 0) {
        CGFloat alpha = -offsetY / SYSTEMTOP_HEIGHT;
        [self setNavAlpha:alpha];
    } else {
        [self setNavAlpha:0.0];
    }
}

- (void)setNavAlpha:(CGFloat)alpha {
    //修改barTintColor的alpha
    self.navigationController.navigationBar.alpha = alpha;
    //修改navigationBar的第一层subview的alpha也可以
    //self.navigationController.navigationBar.subviews.firstObject.alpha = alpha;
    //不起作用
    //self.navigationController.navigationBar.barTintColor = [UIColor colorWithRed:1.0 green:0.5 blue:0.0 alpha:alpha];
}
```
