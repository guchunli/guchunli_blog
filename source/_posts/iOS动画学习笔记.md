---
title: iOS动画学习笔记
date: 2017-07-05 15:15:27
categories: 笔记
tags: [iOS,动画]
toc: true
---

# UIView动画
## UIView的属性
* frame
* bounds
* center
* transform
* alpha
<!--more-->
* backgroundColor
* contentStretch

### transform
* view.transform 和 view.layer.transform的区别：
    - view.transform：二维，`CGAffineTransform transform`。
    - view.layer.transform：3D，`CATransform3D transform`。
    
* CGAffineTransform:
```
//还原
CGAffineTransformIdentity
//位移仿射(CGFloat tx,CGFloat ty)
CGAffineTransformMakeTranslation
CGAffineTransformTranslate
//旋转仿射(CGFloat angle)
CGAffineTransformMakeRotation
CGAffineTransformRotate
//缩放仿射(CGFloat sx, CGFloat sy)
CGAffineTransformMakeScale
CGAffineTransformScale
```

* CATransform3D
```
//还原
CATransform3DIdentity
//位移3D仿射(CGFloat tx, CGFloat ty, CGFloat tz)
CATransform3DMakeTranslation
CATransform3DTranslation
//旋转3D仿射(CGFloat angle, CGFloat x, CGFloat y, CGFloat z)
CATransform3DMakeRotation
CATransform3DRotation
//缩放3D仿射(CGFloat angle, CGFloat x, CGFloat y, CGFloat z)
CATransform3DMakeScale
CATransform3DScale
//叠加3D仿射效果
CATransform3DConcat(CATransform3D a, CATransform3D b)
//仿射基础3D方法，可以直接做效果叠加
CGAffineTransformMake (sx,shx,shy,sy,tx,ty)
//检查是否有做过仿射3D效果(CATransform3D t)
CATransform3DIsIdentity(transform)
//检查2个3D仿射效果是否相同
CATransform3DEqualToTransform(transform1,transform2)
//3D仿射效果反转（反效果，比如原来扩大，就变成缩小）
CATransform3DInvert(transform)
```

* CGAffineTransform与CGTransform3D的转换
```
//将CGAffinrTransform转化为CATransform3D
CATransform3D CATransform3DMakeAffineTransform (CGAffineTransform m);
//将CATransform3D转换为CGAffineTransform
CGAffineTransform CATransform3DGetAffineTransform (CATransform3D t);
//判断一个CATransform3D是否可以转换为CAAffineTransform
CATransform3DIsAffine (CATransform3D t);
```

* 使用
```
//view.transform
self.redView.transform = CGAffineTransformIdentity;
[UIView animateWithDuration:1.0f animations:^{
self.redView.transform = CGAffineTransformMakeScale(2.0f, 2.0f);
}];

//view.layer.transform
self.redView.layer.transform =  CATransform3DIdentity;
[UIView animateWithDuration:1.0f animations:^{
self.redView.layer.transform = CATransform3DMakeScale(2.0, 2.0, 1.0);
}];

//view.layer.transform
CABasicAnimation *transformAnimation = [CABasicAnimation animationWithKeyPath:@"transform"];
transformAnimation.toValue = [NSValue valueWithCATransform3D:CATransform3DMakeScale(2.0, 2.0, 1.0)];
transformAnimation.beginTime = CACurrentMediaTime();
transformAnimation.duration = 1.0f;
transformAnimation.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
[self.redView.layer addAnimation:transformAnimation forKey:@"transformAnimation"];
```

## UIView动画的实现方式
### begin-commit
```
[UIView beginAnimations:nil context:nil];
[UIView setAnimationDuration:2.0];
[UIView setAnimationDelay:.5];
[UIView setAnimationRepeatCount:5];
//是否禁用动画效果（对象属性依然会被改变，只是没有动画效果）
[UIView setAnimationsEnabled:(BOOL)];
//设置代理
[UIView setAnimationDelegate:self];
//设置动画将开始时代理对象执行的方法
[UIView setAnimationWillStartSelector:(nullable SEL)];
//设置动画结束后调用的代理方法
[UIView setAnimationDidStopSelector:@selector(animationDidStop:finished:context:)];
/*
UIViewAnimationCurve：
UIViewAnimationCurveEaseInOut,         // 慢进慢出（默认值）
UIViewAnimationCurveEaseIn,            // 慢进
UIViewAnimationCurveEaseOut,           // 慢出
UIViewAnimationCurveLinear             // 匀速
*/
[UIView setAnimationCurve:(UIViewAnimationCurve)];
//设置视图的过渡效果
/* UIViewAnimationTransition:
UIViewAnimationTransitionNone,              //不使用动画
UIViewAnimationTransitionFlipFromLeft,      //从左向右旋转翻页
UIViewAnimationTransitionFlipFromRight,     //从右向左旋转翻页
UIViewAnimationTransitionCurlUp,            //从下往上卷曲翻页
UIViewAnimationTransitionCurlDown,          //从上往下卷曲翻页
cache：是否使用视图缓存，YES：视图在开始和结束时渲染一次；NO：视图在每一帧都渲染
*/
[UIView setAnimationTransition:(UIViewAnimationTransition) forView:(nonnull UIView *) cache:(BOOL)];
```

### Block
```
/*
UIViewAnimationOptions:(可组合使用)
UIViewAnimationOptionLayoutSubviews            //进行动画时布局子控件
UIViewAnimationOptionAllowUserInteraction      //进行动画时允许用户交互
UIViewAnimationOptionBeginFromCurrentState     //从当前状态开始动画
UIViewAnimationOptionRepeat                    //无限重复执行动画
UIViewAnimationOptionAutoreverse               //执行动画回路
UIViewAnimationOptionOverrideInheritedDuration //忽略嵌套动画的执行时间设置
UIViewAnimationOptionOverrideInheritedCurve    //忽略嵌套动画的曲线设置
UIViewAnimationOptionAllowAnimatedContent      //转场：进行动画时重绘视图
UIViewAnimationOptionShowHideTransitionViews   //转场：移除（添加和移除图层的）动画效果
UIViewAnimationOptionOverrideInheritedOptions  //不继承父动画设置

UIViewAnimationOptionCurveEaseInOut            //时间曲线，慢进慢出（默认值）
UIViewAnimationOptionCurveEaseIn               //时间曲线，慢进
UIViewAnimationOptionCurveEaseOut              //时间曲线，慢出
UIViewAnimationOptionCurveLinear               //时间曲线，匀速

UIViewAnimationOptionTransitionNone            //转场，不使用动画
UIViewAnimationOptionTransitionFlipFromLeft    //转场，从左向右旋转翻页
UIViewAnimationOptionTransitionFlipFromRight   //转场，从右向左旋转翻页
UIViewAnimationOptionTransitionCurlUp          //转场，下往上卷曲翻页
UIViewAnimationOptionTransitionCurlDown        //转场，从上往下卷曲翻页
UIViewAnimationOptionTransitionCrossDissolve   //转场，交叉消失和出现
UIViewAnimationOptionTransitionFlipFromTop     //转场，从上向下旋转翻页
UIViewAnimationOptionTransitionFlipFromBottom  //转场，从下向上旋转翻页
*/
[UIView animateWithDuration:(NSTimeInterval) //动画持续时间
delay:(NSTimeInterval) //动画延迟执行的时间
options:(UIViewAnimationOptions) //动画的过渡效果
animations:^{
//执行的动画
}                completion:^(BOOL finished) {
//动画执行提交后的操作
}];
```

* 过渡动画，主要用于UIView进入或者离开视图
```
[UIView transitionWithView:self.view duration:2.f options:UIViewAnimationOptionTransitionFlipFromLeft animations:^{
//动画效果代码
} completion:^(BOOL finished) {
//动画完成代码
}];
```

# Spring Animation API
```
[UIView animateWithDuration:(NSTimeInterval)//动画持续时间
delay:(NSTimeInterval)//动画延迟执行的时间
usingSpringWithDamping:(CGFloat)//震动效果，范围0~1，数值越小震动效果越明显
initialSpringVelocity:(CGFloat)//初始速度，数值越大初始速度越快
options:(UIViewAnimationOptions)//动画的过渡效果(注意：初始速度取值越高而时间较短时，也会出现反弹效果)
animations:^{
//执行的动画
}
completion:^(BOOL finished) {
//动画执行提交后的操作
}];
```

# keyframes动画
* 关键帧动画，支持属性关键帧，不支持路径关键帧
```
/*
UIViewAnimationOptionLayoutSubviews           //进行动画时布局子控件
UIViewAnimationOptionAllowUserInteraction     //进行动画时允许用户交互
UIViewAnimationOptionBeginFromCurrentState    //从当前状态开始动画
UIViewAnimationOptionRepeat                   //无限重复执行动画
UIViewAnimationOptionAutoreverse              //执行动画回路
UIViewAnimationOptionOverrideInheritedDuration //忽略嵌套动画的执行时间设置
UIViewAnimationOptionOverrideInheritedOptions //不继承父动画设置

UIViewKeyframeAnimationOptionCalculationModeLinear     //运算模式 :连续
UIViewKeyframeAnimationOptionCalculationModeDiscrete   //运算模式 :离散
UIViewKeyframeAnimationOptionCalculationModePaced      //运算模式 :均匀执行
UIViewKeyframeAnimationOptionCalculationModeCubic      //运算模式 :平滑
UIViewKeyframeAnimationOptionCalculationModeCubicPaced //运算模式 :平滑均匀
*/
[UIView animateKeyframesWithDuration:(NSTimeInterval)//动画持续时间
delay:(NSTimeInterval)//动画延迟执行的时间
options:(UIViewKeyframeAnimationOptions)//动画的过渡效果
animations:^{
//执行的关键帧动画
}
completion:^(BOOL finished) {
//动画执行提交后的操作
}];
```

* 增加关键帧
```
[UIView addKeyframeWithRelativeStartTime:(double)//动画开始的时间（占总时间的比例）
relativeDuration:(double) //动画持续时间（占总时间的比例）
animations:^{
//执行的动画
}];
```

# 核心动画（Core Animation）
## CALayer
### CALayer的属性
* bounds
* position：设置CALayer在父层中的位置，是以父层的左上角为坐标原点
* anchorPoint：定位点，决定了CALayer身上的哪个点会在position属性所指的位置，x,y的取值范围是0~1。默认值：(0.5,0.5)，所以默认CALayer的中点会在position所指定的位置上。
* RefbackgroundColor(CGColorRef)
* transform(CGTransform3D)
* borderColor(CGColorRef)
* borderWidth
* cornerRadius
* content(id)   //可以给UIView设置背景图片，CGImage格式

```
// 跨框架赋值需要进行桥接
self.view.layer.contents = (__bridge id _Nullable)([UIImage imageNamed:@"123"].CGImage);
```

### 隐式动画
* 每个UIView都默认关联着一个CALayer，这个Layer就是RootLayer。所有手动创建的的非RootLayer都存在着隐式动画。
* 可动画属性：对非RootLayer的部分属性进行修改，默认会自动产生一些动画效果，这些属性称为可动画属性。常见的可动画属性：
    - bounds：缩放动画
    - backgroundColor：渐变动画
    - position：平移动画
* 事务可以关闭隐式动画（变换仍存在）：
```
[CATransaction begin];
// 关闭隐式动画
[CATransaction setDisableActions:YES];
//动画效果
self.redView.layer.anchorPoint = CGPointMake(0, 0);
[UIView animateWithDuration:3.0 animations:^{
self.redView.transform = CGAffineTransformMakeRotation(M_PI);
} completion:^(BOOL finished) {
}];

[CATransaction commit];
```

## 核心动画
### 核心动画的特点
* 核心动画的执行过程是在后台操作的，不会阻塞主线程
* 核心动画直接作用在CALayer上
* 属于Quartz Core框架的API
* 不支持交互

### 核心动画的使用步骤
* 初始化一个CAAnimation的对象，并设置一些动画相关属性
* 通过调用CALayer的`addAnimation:forKey:`增加CAAniamtion对象到CALayer中，开始执行动画
* 通过调用CALayer的`removeAnimation:forKey:`停止CALayer的动画

### CAAnimation
* CAAnimation是所有动画对象的父类，是抽象类，不可直接使用，应该使用它具体的子类
* CAAnimation继承结构
    - CAAnimation->CAMediaTiming协议
        * CAPropertyAnimation
            - CABasicAnimation
            - CAKeyframeAnimation
        * CAAnimationGroup
        * CATransition

* CAAnimation——动画填充模式（fillMode）
`要想fillMode有效，最好设置removedOnCompletion = NO`
```
//    创建动画
CABasicAnimation *anim = [CABasicAnimation animation];;
//    设置动画对象
//    keyPath决定了执行怎样的动画,调用layer的哪个属性来执行动画
//    position:平移
anim.keyPath = @"position";
//    包装成对象
anim.fromValue = [NSValue valueWithCGPoint:CGPointMake(0, 0)];;
anim.toValue = [NSValue valueWithCGPoint:CGPointMake(200, 300)];
anim.duration = 2.0;

//    让图层保持动画执行完毕后的状态
//    执行完毕以后不要删除动画
anim.removedOnCompletion = NO;
//    保持最新的状态
anim.fillMode = kCAFillModeForwards;

//    添加动画
[self.redView.layer addAnimation:anim forKey:nil];
```

fillMode属性值：
    - kCAFillModeRemoved 默认值，也就是说当动画开始前和动画结束后，动画对layer都没有影响，动画结束后，layer会恢复到之前的状态
    - kCAFillModeForwards 当动画结束后，layer会一直保持着动画最后的状态
    - kCAFillModeBackwards 在动画开始前，只需要将动画加入了一个layer，layer便立即进入动画的初始状态并等待动画开始。
    - kCAFillModeBoth 动画加入后开始之前，layer便处于动画初始状态，动画结束后layer保持动画最后的状态

* CAAnimation——速度控制函数（timingFunction）
CAMediaTimingFunction属性值：
    - kCAMediaTimingFunctionLinear（线性）
    - kCAMediaTimingFunctionEaseIn（渐进）
    - kCAMediaTimingFunctionEaseOut（渐出）
    - kCAMediaTimingFunctionEaseInEaseOut（渐进渐出）
    
```
//设置动画的执行节奏
anim.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionLinear];
```

* CAAnimation——动画代理方法（delegate）
```
//CAAnimationDelegate
- (void)animationDidStart:(CAAnimation *)anim;
- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)flag;
```

* CALayer上动画的暂停和恢复
```
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{
    static NSInteger count = 0;
    if (count++%2 == 0) {
        [self pauseLayer:self.redView.layer];
    }else{
        [self resumeLayer:self.redView.layer];
}
}

//停止
-(void)pauseLayer:(CALayer*)layer
{
    CFTimeInterval pausedTime = [layer convertTime:CACurrentMediaTime() fromLayer:nil];
    //让CALayer的时间停止走动
    layer.speed = 0.0;
    //让CALayer的时间停留在pausedTime这个时刻
    layer.timeOffset = pausedTime;
}
//恢复
-(void)resumeLayer:(CALayer*)layer
{
    CFTimeInterval pausedTime = layer.timeOffset;
    //1. 让CALayer的时间继续行走
    layer.speed = 1.0;
    //2. 取消上次记录的停留时刻
    layer.timeOffset = 0.0;
    //3. 取消上次设置的时间
    layer.beginTime = 0.0;
    //4. 计算暂停的时间(这里也可以用CACurrentMediaTime()-pausedTime)
    CFTimeInterval timeSincePause = [layer convertTime:CACurrentMediaTime() fromLayer:nil] - pausedTime;
    //5. 设置相对于父坐标系的开始时间(往后退timeSincePause)
    layer.beginTime = timeSincePause;
}
```

#### CAPropertyAnimation 抽象类
* 通过指定CALayer的一个属性名称为keyPath（NSString类型），并且对CALayer的这个属性值进行修改，达到响应动画效果。
`keyPath是CALayer的可动画属性`

#### CABasicAnimation
```
//创建动画对象
CABasicAnimation *anim = [CABasicAnimation animation];

//设置动画属性
anim.keyPath = @"position.y";
anim.toValue = @300;

//动画提交时,会自动删除动画
anim.removedOnCompletion = NO;
//设置动画最后保持状态
anim.fillMode = kCAFillModeForwards;

//添加动画对象
[self.redView.layer addAnimation:anim forKey:nil];
```

#### CAKeyframeAnimation 关键帧动画
* 与CABasicAnimation的区别：
    - CABasicAnimation：只能从fromValue变到toValue（可看做只有2个关键帧的CAKeyframeAnimation）
    - CAKeyframeAnimation：会使用NSArray保存这些数值

```
//    创建动画
CAKeyframeAnimation *anim = [CAKeyframeAnimation animation];;
//    设置动画对象
//  keyPath决定了执行怎样的动画,调整哪个属性来执行动画
anim.keyPath = @"position";
NSValue *v1 = [NSValue valueWithCGPoint:CGPointMake(100, 0)];
NSValue *v2 = [NSValue valueWithCGPoint:CGPointMake(200, 0)];
NSValue *v3 = [NSValue valueWithCGPoint:CGPointMake(300, 0)];
NSValue *v4 = [NSValue valueWithCGPoint:CGPointMake(400, 0)];

anim.values = @[v1,v2,v3,v4];
anim.duration = 2.0;
//    让图层保持动画执行完毕后的状态
//    状态执行完毕后不要删除动画
anim.removedOnCompletion = NO;
//    保持最新的状态
anim.fillMode = kCAFillModeForwards;

//    添加动画
[self.redView.layer addAnimation:anim forKey:nil];
```

* 根据路径创建动画
```
//    创建动画
CAKeyframeAnimation *anim = [CAKeyframeAnimation animation];

anim.keyPath = @"position";
anim.removedOnCompletion = NO;
anim.fillMode = kCAFillModeForwards;
anim.duration = 2.0;

//    创建一个路径
CGMutablePathRef path = CGPathCreateMutable();
//    路径的范围
CGPathAddEllipseInRect(path, NULL, CGRectMake(100, 100, 200, 200));
//    添加路径
anim.path = path;
//    释放路径(带Create的函数创建的对象都需要手动释放,否则会内存泄露)
CGPathRelease(path);
//    添加到View的layer
[self.redView.layer addAnimation:anim forKey:nil];
```

#### CAAnimationGroup 动画组
```
CAAnimationGroup *group = [CAAnimationGroup animation];

//    创建旋转动画对象
CABasicAnimation *retate = [CABasicAnimation animation];
//    layer的旋转属性
retate.keyPath = @"transform.rotation";
//    角度
retate.toValue = @(M_PI);

//    创建缩放动画对象
CABasicAnimation *scale = [CABasicAnimation animation];
//    缩放属性
scale.keyPath = @"transform.scale";
//    缩放比例
scale.toValue = @(0.0);
//    添加到动画组当中
group.animations = @[retate,scale];
//           执行动画时间
group.duration = 2.0;
//
group.repeatCount = MAXFLOAT;
//
group.autoreverses = YES;
//    执行完以后不要删除动画
group.removedOnCompletion = NO;
//          保持最新的状态
group.fillMode = kCAFillModeForwards;

[self.redView.layer addAnimation:group forKey:nil];
```

* 播放gif图
```
- (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event{

NSArray *imageArray = [self getImageArrayWithGIFNameWit:@"aisi"];
self.imageView.animationImages = imageArray;
self.imageView.animationDuration = 3;
self.imageView.animationRepeatCount = MAXFLOAT;
[self.imageView startAnimating];

dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(5.0 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{

[_imageView stopAnimating];
});
}

- (NSArray<UIImage *> *)getImageArrayWithGIFNameWit:(NSString *)imageName {

NSMutableArray *imageArray = [NSMutableArray array];
NSString *path = [[NSBundle mainBundle] pathForResource:imageName ofType:@"gif"];
NSData *data = [NSData dataWithContentsOfFile:path];

if (!data) {
NSLog(@"图片不存在!");
return nil;
}

CGImageSourceRef source = CGImageSourceCreateWithData((__bridge CFDataRef)data, NULL);

size_t count = CGImageSourceGetCount(source);

if (count <= 1) {
[imageArray addObject:[[UIImage alloc] initWithData:data]];
}else {

for (size_t i = 0; i < count; i++) {
CGImageRef image = CGImageSourceCreateImageAtIndex(source, i, NULL);

[imageArray addObject:[UIImage imageWithCGImage:image scale:[UIScreen mainScreen].scale orientation:UIImageOrientationUp]];

CGImageRelease(image);
}
}

CFRelease(source);

return imageArray;
}
```

#### CATransition 转场动画
* `作用对象是父视图，过渡效果体现在父视图上`
* 创建转场动画
    - 创建动画对象：[CATransition animation];
    - 设置动画属性值
    - 添加到需要转场动画的图层上： [ layer addAimation:animation forKer:nil];
* 动画属性
    - type
    - subtype
    - startProgress
    - endProgress

```
/*
type
fade : 交叉淡化过渡
push : 新视图把旧视图推出去
moveIn: 新视图移到旧视图上面
reveal: 将旧视图移开,显示下面的新视图
cube : 立方体翻滚效果
oglFlip : 上下左右翻转效果
suckEffect : 收缩效果，如一块布被抽走
rippleEffect: 水滴效果
pageCurl : 向上翻页效果
pageUnCurl : 向下翻页效果
cameraIrisHollowOpen : 相机镜头打开效果
cameraIrisHollowClos : 相机镜头关闭效果
*/

CATransition *anim = [CATransition animation];
//转场类型
anim.type = @"cube";
//动画执行时间
anim.duration = 0.5;
//动画执行方向
anim.subtype = kCATransitionFromLeft;
//动画从哪个点开始
//    anim.startProgress = 0.2;
//    anim.endProgress = 0.3;
//添加到View的layer
[self.redView.layer addAnimation:anim forKey:nil];
```

##### 使用UIView实现转场动画
* 单个视图的过渡效果
```
[UIView transitionWithView:(nonnull UIView *)
duration:(NSTimeInterval)
options:(UIViewAnimationOptions)
animations:^{
//执行的动画
}
completion:^(BOOL finished) {
//动画执行提交后的操作
}];
```

* 双视图的过渡效果
```
[UIView transitionFromView:(nonnull UIView *) toView:(nonnull UIView *) duration:(NSTimeInterval) options:(UIViewAnimationOptions) completion:^(BOOL finished) {
//动画执行提交后的操作
}];

//相当于
//[fromView.superview addSubview:toView];
//[fromView removeFromSuperview];
```

##### 自定义转场动画

[iOS动画，绝对够分量！](https://www.jianshu.com/p/9fa025c42261)
[iOS 动画 —— transform](https://www.jianshu.com/p/3bc427f0dd56)
