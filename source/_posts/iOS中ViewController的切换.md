---
title: iOS中ViewController的切换
copyright: true
date: 2019-08-23 11:09:06
categories: 笔记
tags:
toc:
---

* iOS中ViewController的切换方式一般有：push,modal,tabbar,childViewController几种。
<!--more-->
## push
### 视图控制容器（View Controller Container）
UINavgationController、UITabBarController、UISplitViewController等都属于view controller container这些类的共性是都有一个类型为数组的对象viewControllers属性，用于保存一组视图控制器。
任何view controller container对象都可以通过viewControllers属性来访问子对象（视图），而子对象也可以通过navigationController, tabbarController, splitViewController, parentViewController找到相应的view controller container。

* 情形：A->B->C，A push B，B push C，现在要从C直接回到A中
```
// 直接修改ViewControllers堆栈信息：@[vcA,vcB,vcC]->@[vcA]
[self.navigationController setViewControllers:@[vcA] animated:YES];
```

## modal 模态视图
### present
* present：当应用以模态形式显示一个视图时，即通过presentViewController: animated: completion:推出的视图，负责显示的控制器将是视图控制容器（View Controller Container）最顶部的视图，而负责推出的视图。
* presentingViewController：A present B，B的presentingViewController就是A
* presentedViewController：A present B，A的presentedViewController就是B

* 情形： tabbarController->navigationController->vcA，vcA present vcB
```
[vcA presentViewController:vcB animated:YES completion:nil];
NSLog(@"presenting:%@---prensented:%@",self.presentingViewController,self.presentedViewController);
// presenting:<UITabBarController: 0x7f90a9013400>---prensented:(null)
```

若想让B的presentingViewController变成A：
```
vcB.modalPresentationStyle = UIModalPresentationCurrentContext;
self.definesPresentationContext = YES;
[vcA presentViewController:vcB animated:YES completion:nil];
```

### dismiss
如果 A present B，那么A就有责任dismiss B，如果在B中调用[self  dismissViewControllerAnimated...]就会A中发送dismiss，最终会由有A进行dismiss,相当于在B中执行[self.presentingViewController dismissViewControllerAnimated...]

* 情形：A->B->C(A present B,B present C)
C dismiss： 结果就是B modal 出来的C被移除。B present C，那么B就有责任dismiss C，如果在C中调用[self dismissViewControllerAnimated...]就会B中发送dismiss，最终会由B进行dismiss，相当于在C中执行[self.presentingViewController dismissViewControllerAnimated...]。
B dismiss： 结果就是B modal 出来的 C 被移除。B自身并不会被移除。
A dismiss：A modal 出来的B就会被移除，B modal 出的C因为B被移除了，C自己也就被移除了。不过移除动画只有B被移除的动画被显示，C的移除动画是没有的。

### 使用
```
vcB

@protocol ModalViewControllerDelegate <NSObject>
-(void) modalViewControllerDidClickedDismissButton:(ModalViewController *)viewController;

@property (nonatomic, weak) id<ModalViewControllerDelegate> delegate;

if (self.delegate && [self.delegate respondsToSelector:@selector(modalViewControllerDidClickedDismissButton:)]) {
[self.delegate modalViewControllerDidClickedDismissButton:self];
}
```

```
vcA

//遵循<ModalViewControllerDelegate>，实现协议方法
-(void)modalViewControllerDidClickedDismissButton:(ModalViewController *)viewController {
[self dismissViewControllerAnimated:YES completion:nil];
}
```

### 自定义模态动画效果
1.新建一个类来实现自定义动画
```
// 遵循<UIViewControllerAnimatedTransitioning>，实现协议方法
- (NSTimeInterval)transitionDuration:(id <UIViewControllerContextTransitioning>)transitionContext
{
return 0.8f;
}

- (void)animateTransition:(id <UIViewControllerContextTransitioning>)transitionContext
{
// 1. Get controllers from transition context
UIViewController *toVC = [transitionContext viewControllerForKey:UITransitionContextToViewControllerKey];

// 2. Set init frame for toVC
CGRect screenBounds = [[UIScreen mainScreen] bounds];
CGRect finalFrame = [transitionContext finalFrameForViewController:toVC];
toVC.view.frame = CGRectOffset(finalFrame, 0, screenBounds.size.height);

// 3. Add toVC's view to containerView
UIView *containerView = [transitionContext containerView];
[containerView addSubview:toVC.view];

// 4. Do animate now
NSTimeInterval duration = [self transitionDuration:transitionContext];
[UIView animateWithDuration:duration
delay:0.0
usingSpringWithDamping:0.6
initialSpringVelocity:0.0
options:UIViewAnimationOptionCurveLinear
animations:^{
toVC.view.frame = finalFrame;
} completion:^(BOOL finished) {
// 5. Tell context that we completed.
[transitionContext completeTransition:YES];
}];
}
```

2.使用这个自定义动画
```
// 1.初始化自定义动画的类对象
// 2.遵循UIViewControllerTransitioningDelegate
mvc.transitioningDelegate = self;
[self presentViewController:mvc animated:YES completion:nil];

// 3.实现协议方法
- (id <UIViewControllerAnimatedTransitioning>)animationControllerForPresentedController:(UIViewController *)presented presentingController:(UIViewController *)presenting sourceController:(UIViewController *)source {
    return self.presentAnimation;
}
```

## childViewController
### 添加childViewController
[self addChildViewController: childVC]： 会调用 childVC 的 willMoveToParentViewController: 方法和 didMoveToParentViewController:方法
[self.addSubview:childVC.view]会调用childVC的viewDidLoad方法。
```
SecondViewController *secondVC = [[SecondViewController alloc]init];
secondVC.view.frame = self.view.bounds;
[self addChildViewController:secondVC];//1 vc
[self.view addSubview:secondVC.view];//2 view
[secondVC didMoveToParentViewController:self];//3 didmove
```

### 移除一个Controller:
```
//移除childVC，但在removeFromParentViewController：方法前不会调用willMoveToParentViewController:nil 方法，所以需要显式调用
[childVC willMoveToParentViewController:nil]; //1 willmove
[childVC.view removeFromSuperview]; //2 view
[childVC removeFromParentViewController]; //3 vc
```

### viewWillAppear & viewDidAppear
Controller里面的viewWillAppear:(BOOL)animated在subview真正加到父view之前调用，viewDidAppear:(BOOL)animated在真正被add到父view之后调用，视图消失也是一样。

[towCol beginAppearanceTransition:YES animated:YES]触发towCol的viewWillAppear，[towCol beginAppearanceTransition:NO animated:YES]触发towCol的viewWillDisappear，和他们配套的[towCol endAppearanceTransition]分别触发viewDidAppear和viewDidDisappear

childViewController被添加后以下方法的执行顺序为：
```
willMoveToParentViewController:
viewDidLoad
viewWillAppear:
didMoveToParentViewController:
viewDidAppear:
```
childViewController被移除后以下方法的执行顺序为：
```
willMoveToParentViewController:
viewWillDisappear:
didMoveToParentViewController:
viewDidDisappear:
```

### 常见问题
<!--2.如果把childVC.view添加到collectionView的cell上，当点击cell时会无法触发navigationController的push或者present操作。-->
