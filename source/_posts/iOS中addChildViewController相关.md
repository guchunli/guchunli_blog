---
title: iOS中addChildViewController相关
date: 2018-08-01 14:26:10
categories: 笔记
tags: [addChildViewController]
toc:
---

## 添加Controller
```
TowController *towCol = [[TowController alloc]init];
towCol.view.frame = self.view.bounds;
[self addChildViewController:towCol];//1
[self.view addSubview:towCol.view];//2
//addChildViewController 会调用 [child willMoveToParentViewController:self] 方法，但是不会调用 didMoveToParentViewController:方法，官方建议显示调用
[towCol didMoveToParentViewController:self];//3
```
<!--more-->
## 移除一个Controller:
```
////移除oldController，但在removeFromParentViewController：方法前不会调用willMoveToParentViewController:nil 方法，所以需要显示调用
[towCol willMoveToParentViewController:nil]; //1
[towCol.view removeFromSuperview]; //2
[towCol removeFromParentViewController]; //3
```

## viewWillAppear & viewDidAppear
Controller里面的viewWillAppear:(BOOL)animated在subview真正加到父view之前调用，viewDidAppear:(BOOL)animated在真正被add到父view之后调用，视图消失也是一样。

[towCol beginAppearanceTransition:YES animated:YES]触发towCol的viewWillAppear，[towCol beginAppearanceTransition:NO animated:YES]触发towCol的viewWillDisappear，和他们配套的[towCol endAppearanceTransition]分别触发viewDidAppear和viewDidDisappear

## 常见问题
1.[self.scrollView addSubview:childVC.view]会调用childVC的viewDidLoad方法。
<!--2.如果把childVC.view添加到collectionView的cell上，当点击cell时会无法触发navigationController的push或者present操作。-->



