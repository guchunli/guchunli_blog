---
title: iOS的UIView动画学习笔记
date: 2017-07-05 15:15:27
categories: 学习
tags: [iOS,动画]
---

1.begin-commit
<!--more-->
```
[UIView beginAnimations:nil context:nil];
[UIView setAnimationDuration:2.0];
[UIView setAnimationDelay:.5];
[UIView setAnimationRepeatCount:5];
//设置代理
[UIView setAnimationDelegate:self];
//设置动画结束后调用的代理方法
[UIView setAnimationDidStopSelector:@selector(animationDidStop:finished:context:)];
//动画效果代码
view1.alpha = 0;
[UIView commitAnimations];
```

2.animate
```
[UIView animateWithDuration:2.0 animations:^{
    view1.alpha = 0;
} completion:^(BOOL finished) {
    if (finished) {
        view1.alpha = 1;
    }
}];
```

3.transition
过渡动画，主要用于UIView进入或者离开视图
```
[UIView transitionWithView:self.view duration:2.f options:UIViewAnimationOptionTransitionFlipFromLeft animations:^{
    //动画效果代码
} completion:^(BOOL finished) {
    //动画完成代码
}];
```



