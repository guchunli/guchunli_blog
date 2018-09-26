---
title: iOS提高界面流畅度的技巧
date: 2017-12-19 19:10:30
categories: 笔记
tags: [优化]
toc:
---

## 屏幕显示图像原理
App 主线程在 CPU 中计算显示内容，比如视图的创建、布局计算、图片解码、文本绘制等。随后 CPU 会将计算好的内容提交到 GPU 去，由 GPU 进行变换、合成、渲染。随后 GPU 会把渲染结果提交到帧缓冲区去，等待信号到来时显示到屏幕上。
<!--more-->
## CPU资源消耗原因和解决方案
### 对象创建
对象的创建会分配内存、调整属性、甚至还有读取文件等操作。
* 尽量用轻量的对象代替重量的对象
* 使用storyboard创建对象比通过代码消耗资源多
* 尽量推迟对象创建的时间，并把对象的创建分散到多个任务中去
* 能复用的对象尽量使用复用

### 对象调整
* 尽量减少不必要的属性修改，UIView 的关于显示相关的属性（比如 frame/bounds/transform）等实际上都是 CALayer 属性映射来的
* 尽量避免调整视图层次，添加或移除视图

### 对象销毁
* 如果对象可以放到后台线程去释放，那就挪到后台线程去。`把对象捕获到 block 中，然后扔到后台队列去随便发送个消息以避免编译器警告，就可以让对象在后台线程销毁了。`
```
NSArray *tmp = self.array;
self.array = nil;
dispatch_async(queue, ^{
[tmp class];
});
```

### 布局计算
* 视图布局的计算是APP中最常见消耗CPU的地方，尽量在`后台线程`计算好视图布局并对视图布局进行`缓存`，尽量`提前`算好布局，在需要时一次性调整好对应属性，而不要多次，频繁的计算、调整这些属性

### autolayout
* autolayout对于复杂视图来说常常会产生严重的性能问题，尽量手动调整 frame 等属性，可以使用category来快捷地获取视图的width,height等属性。

### 文本计算
* 可以参考UILabel 内部的实现方式：用 [NSAttributedString boundingRectWithSize:options:context:] 来计算文本宽高，用 -[NSAttributedString drawWithRect:options:context:] 来绘制文本，注意需要放到后台线程进行以避免阻塞主线程。

### 文本渲染
* 用 TextKit 或最底层的 CoreText 对文本异步绘制。CoreText 对象占用内存较少，可以缓存下来以备稍后多次渲染。屏幕上能看到的所有文本内容控件，在底层都是通过 CoreText 排版、绘制为 Bitmap 显示的。

### 图片解码
当你用 UIImage 或 CGImageSource 的那几个方法创建图片时，图片数据并不会立刻解码。图片设置到 UIImageView 或者 CALayer.contents 中去，并且 CALayer 被提交到 GPU 前，CGImage 中的数据才会得到解码。这一步是发生在主线程的，并且不可避免。如果想要绕开这个机制，常见的做法是在后台线程先把图片绘制到 CGBitmapContext 中，然后从 Bitmap 直接创建图片。目前常见的网络图片库都自带这个功能。

### 图片绘制
图像的绘制通常是指用那些以 CG 开头的方法把图像绘制到画布中，然后从画布创建图片并显示这样一个过程。这个最常见的地方就是 [UIView drawRect:] 里面了。由于 CoreGraphic 方法通常都是线程安全的，所以图像的绘制可以很容易的放到后台线程进行。一个简单异步绘制的过程大致如下（实际情况会比这个复杂得多，但原理基本一致）：
```
- (void)display {
    dispatch_async(backgroundQueue, ^{
        CGContextRef ctx = CGBitmapContextCreate(...);
        // draw in context...
        CGImageRef img = CGBitmapContextCreateImage(ctx);
        CFRelease(ctx);
        dispatch_async(mainQueue, ^{
            layer.contents = img;
        });
    });
}
```

## GPU资源消耗原因和解决方案
* GPU的作用：接收提交的纹理和顶点描绘，应用变换，混合和渲染，并且输出到屏幕上。

### 纹理的渲染
* 尽量减少在短时间内大量图片的显示，尽可能多张图片合成为一张进行显示。
* 尽量不要让图片和视图的大小超过4096*4096

### 视图的混合
* 尽量减少视图数量和层次，并在不透明的视图里标明 opaque 属性以避免无用的 Alpha 通道合成

### 图形的生成
* CALayer 的 border、圆角、阴影、遮罩（mask），CASharpLayer 的矢量图形显示，通常会触发离屏渲染，离屏渲染通常发生在 GPU 中，可以尝试开启 CALayer.shouldRasterize 属性，但这会把原本离屏渲染的操作转嫁到 CPU 上去，也可以用一张已经绘制好的圆角图片覆盖到原本视图上面来模拟相同的视觉效果。最彻底的解决办法，就是把需要显示的图形在后台线程绘制为图片，避免使用圆角、阴影、遮罩等属性。

## AsyncDisplayKit(ASDK)
ASDK 认为，阻塞主线程的任务，主要分为三大类：
1.文本宽高计算，视图布局计算
2.文本渲染，图片解码，图形绘制
3.对象创建，对象调整，对象销毁

与 UIView 和 CALayer 不同，ASDisplayNode 是线程安全的，它可以在后台线程创建和修改。

* ASDK 的图层预合成：那些并不需要响应触摸事件，也不需要进行动画和位置调整的 sub-layer，可以合成渲染为一张图片
* ASDK 异步并发操作：利用 GCD 异步并发执行
* Runloop 任务分发

## 预排版
* 当获取到 API JSON 数据后，把每条 Cell 需要的数据都在后台线程计算并封装为一个布局对象 CellLayout。
* FDTemplateLayoutCell

## product->profile


# 提高列表滚动流畅度
## 避免重复多次计算 cell 行高
* 获取数据
```
for (InfoModel *model in data) {
    FrameModel *frameModel = [FrameModel new];
    frameModel.model = model;
    [self.data addObject:frameModel];
}
[self.tabView reloadData];
```

* 计算 frame 、cell 行高
```
@interface FrameModel : NSObject

@property (assign, nonatomic, readonly) CGRect titleFrame;
@property (assign, nonatomic, readonly) CGFloat cellHeight;
@property (strong, nonatomic) InfoModel *model;

@end

@implementation FrameModel

- (void)setModel:(InfoModel *)model {
if (!model) return;

_model = model;

CGFloat maxLayout = ([UIScreen mainScreen].bounds.size.width - 20.f);
CGFloat bottom = 4.f;

//title
CGFloat titleX = 10.f;
CGFloat titleY = 10.f;
CGSize titleSize = [model.title boundingRectWithSize:CGSizeMake(maxLayout, CGFLOAT_MAX) options:NSStringDrawingUsesLineFragmentOrigin|NSStringDrawingUsesFontLeading attributes:@{NSFontAttributeName : Font(16.f)} context:nil].size;
_titleFrame = CGRectMake(titleX, titleY, titleSize.width, titleSize.height);

//cell Height
_cellHeight = (CGRectGetMaxY(_titleFrame) + bottom);
}

@end
```

* 行高取值
```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    TableCell *cell = [tableView dequeueReusableCellWithIdentifier:@"identifier" forIndexPath:indexPath];
    FrameModel *frameModel = self.data[indexPath.row];
    cell.frameModel = frameModel;
    return cell;
}

- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath {
    FrameModel *frameModel = self.data[indexPath.row];
    return frameModel.cellHeight;
}
```

* 控件赋值
```
- (void)setFrameModel:(FrameModel *)frameModel {
    if (!frameModel) return;

    _frameModel = frameModel;

    InfoModel *model = frameModel.model;

    self.titleLabel.frame = frameModel.titleFrame;
    self.titleLabel.text = model.title;
}
```

## 文本异步渲染
用 TextKit 或最底层的 CoreText 对文本异步绘制，支持文本异步渲染也有现成的库 YYText 。* 计算 frame 、cell 行高
```
@interface FrameModel : NSObject

@property (assign, nonatomic, readonly) CGRect titleFrame;
@property (strong, nonatomic, readonly) YYTextLayout *titleLayout;
@property (assign, nonatomic, readonly) CGFloat cellHeight;
@property (strong, nonatomic) InfoModel *model;

@end

@implementation FrameModel

- (void)setModel:(InfoModel *)model {
if (!model) return;

_model = model;

CGFloat maxLayout = ([UIScreen mainScreen].bounds.size.width - 20.f);
CGFloat bottom = 4.f;

//title
NSMutableAttributedString *title = [[NSMutableAttributedString alloc] initWithString:entity.title];
title.yy_font = Font(16.f);
title.yy_color = [UIColor blackColor];

YYTextContainer *titleContainer = [YYTextContainer containerWithSize:CGSizeMake(maxLayout, CGFLOAT_MAX)];
_titleLayout = [YYTextLayout layoutWithContainer:titleContainer text:title];

CGFloat titleX = 10.f;
CGFloat titleY = 10.f;
CGSize titleSize = _titleLayout.textBoundingSize;
//CGSize titleSize = [model.title boundingRectWithSize:CGSizeMake(maxLayout, CGFLOAT_MAX) options:NSStringDrawingUsesLineFragmentOrigin|NSStringDrawingUsesFontLeading attributes:@{NSFontAttributeName : Font(16.f)} context:nil].size;
_titleFrame = (CGRect){titleX,titleY,CGSizeMake(titleSize.width, titleSize.height)};

//cell Height
_cellHeight = (CGRectGetMaxY(_titleFrame) + bottom);
}

@end
```

* UITableViewCell 处理
```
- (instancetype)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier {
    self = [super initWithStyle:style reuseIdentifier:reuseIdentifier];
    if (!self) return nil;

    YYLabel *title = [YYLabel new];
    title.displaysAsynchronously = YES; //开启异步渲染
    title.ignoreCommonProperties = YES; //忽略属性
    title.layer.borderColor = [UIColor brownColor].CGColor;
    title.layer.cornerRadius = 1.f;
    title.layer.borderWidth = 1.f;
    [self.contentView addSubview:_titleLabel = title];

    return self;
}
```

* 控件赋值
```
- (void)setFrameModel:(FrameModel *)frameModel {
if (!frameModel) return;

_frameModel = frameModel;

self.titleLabel.frame = frameModel.titleFrame;
self.titleLabel.textLayout = model.titleLayout; //直接取 YYTextLayout
//InfoModel *model = frameModel.model;
//self.titleLabel.text = model.title;
}
```

## 离屏渲染
* On-Screen Rendering
当前屏幕渲染，指的是在当前用于显示的屏幕缓冲区中进行渲染操作。

* Off-Screen Rendering
离屏渲染，指的是 GPU 或 CPU在当前屏幕缓冲区以外新开辟一个缓冲区进行渲染操作。过程中需要切换 contexts (上下文环境),先从当前屏幕切换到离屏的contexts，渲染结束后，又要将 contexts 切换回来，而切换过程十分耗费性能。

GPU 产生的离屏渲染主要是当 CALayer 使用圆角，阴影，遮罩等属性的的时候，图层属性的混合体被指定为在未预合成之前不能直接在屏幕中渲染，则过程中需要进行离屏渲染。
实际项目中 CPU 产生的离屏渲染主要由Core Graphics API(核心绘图)的使用导致。

### 离屏渲染的检测手段
模拟器的 debug -> 选取 color Offscreen-Rendered.

### 离屏渲染的优化手段
#### shadows（阴影）
解决方案:设置阴影后，设置CALayer的 shadowPath
```
view.layer.shadowPath = [UIBezierPath pathWithCGRect:view.bounds].CGPath;
```

#### masksToBounds = true +  cornerRadius > 0
解决方案:后台绘制圆角图片，前台进行设置
```
- (void)setCircleImage
{
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
UIImage * circleImage = [image imageWithCircle];
dispatch_async(dispatch_get_main_queue(), ^{
imageView.image = circleImage;
});
});
}


#import "UIImage+Addtions.h"
@implementation UIImage (Addtions)
//返回一张圆形图片
- (instancetype)imageWithCircle
{
UIGraphicsBeginImageContextWithOptions(self.size, NO, 0);
UIBezierPath *path = [UIBezierPath bezierPathWithOvalInRect:CGRectMake(0, 0, self.size.width, self.size.height)];
[path addClip];
[self drawAtPoint:CGPointZero];
UIImage *image = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
return image;
}
```

对于 contents 无内容或者内容的背景透明(无涉及到圆角以外的区域)的layer，直接设置layer的 backgroundColor 和 cornerRadius 属性来绘制圆角。
```
//不要直接设置控件的 backgroundColor
//部分控件(如 UILabel )设置背景色是对CALayer的 contents 设置颜色而不是 backgroundColor 
label.layer.backgroundColor = aColor
label.layer.cornerRadius = 5
```

使用混合图层，在layer上方叠加相应mask形状的半透明layer
```
sublayer.contents = (id)[UIImage imageNamed:@"xxx"].CGImage;
[view.layer addSublayer:sublayer];
```

####  mask（遮罩）
解决方案:
* 不使用mask
* 使用混合图层，具体同上方圆角第二解决方案

#### allowsGroupOpacity（组不透明）
开启CALayer的 allowsGroupOpacity 属性后，子 layer 在视觉上的透明度的上限是其父 layer 的 opacity (对应UIView的 alpha )，并且从 iOS 7 以后默认全局开启了这个功能，这样做是为了让子视图与其容器视图保持同样的透明度。
解决方案:关闭 allowsGroupOpacity 属性，按产品需求自己控制layer透明度。

#### edge antialiasing（抗锯齿）
解决方案:不设置 allowsEdgeAntialiasing 属性为YES(默认为NO)

#### CALayer离屏渲染终极解决方案
当视图内容是静态不变时，设置 shouldRasterize(光栅化)为YES，此方案最为实用方便。
```
view.layer.shouldRasterize = true;
view.layer.rasterizationScale = view.layer.contentsScale;
```
但当视图内容是动态变化(如后台下载图片完毕后切换到主线程设置)时，使用此方案反而为增加系统负荷。

#### CPU离屏渲染的优化手段
将Core Graphics API(核心绘图)的绘制操作放到后台线程中进行。



# 解决界面卡顿

## 离屏渲染
### 界面显示的原理
iOS设备通常是60fps(每秒60帧)，也就是说两帧相隔的时间是1/60秒，大概16.7ms。在这16.7ms中，为了显示一帧，需要如下工作：
* CPU计算好各个视图的位置，大小，对图片进行解码等，绘制成纹理交给GPU
* GPU对收到的纹理进行混合，顶点变换，渲染到帧缓冲区
* 每16.7ms,一个时钟信号到达，帧缓冲区取出一帧，显示到屏幕。
也就是说，CPU或者GPU被大量占用的时候，都有可能在16.7ms中没办法完成一帧的绘制，导致时钟信号到来的时候，取得还是上一帧的内容，也就都有可能导致界面卡顿

### 渲染 & 离屏渲染
在iOS中，渲染通常分为CPU和GPU渲染两种，而GPU渲染又分为在GPU缓冲区和非GPU缓冲区两种
* CPU渲染（软件渲染）,CPU绘制成bitmap，交给GPU
* GPU渲染（硬件渲染） 
    * GPU缓冲区渲染
    * 非GPU缓冲区渲染（额外开辟缓冲区）
通常，CPU渲染，和GPU非帧缓冲区内渲染统称为离屏渲染。因为，CPU和帧缓冲区是为图形图像显示做了高度优化的，速度较快。

什么情况下会触发离屏幕渲染？

用CoreGraphics的CGContext绘制的
在drawRect中绘制的，即使drawRect是空的
Layer具有Mask（比如圆角）或者Shadow
Layer的隔栅化shouldRasterize为True
文本(UILabel,UITextfield,UITextView,CoreText,UITextLayer等)

### CoreGraphics VS CALayer
上文提到了，CoreGraphics通常是CPU渲染成bitmap交给GPU，假如频繁的大量的绘制出现，往往会导致界面卡顿。而CALayer是对GPU做过优化的，能够硬件加速。所以，对于性能要求较高的绘制，尝试用CALayer替代CoreGraphics

## Core Animation(获取图形绘制情况，FPS，离屏渲染等)
* Color Blended Layers：图层混合情况，没有混合的部分会是绿色，混合最严重的部分会是红色
* Color Hits Green and Misses Red：隔栅化情况，当使用shouldRasterize属性的时候，耗时的图层绘制会被缓存，然后当做一个简单的扁平图片呈现。当缓存无法使用必须重建的时候，会被高亮为红色。
* Color Copied Images：拷贝图片情况，有时候寄宿图片的生成意味着Core Animation被强制生成一些图片，然后发送到渲染服务器，而不是简单的指向原始指针。这个选项把这些图片渲染成蓝色。复制图片对内存和CPU使用来说都是一项非常昂贵的操作，所以应该尽可能的避免。
* Color Misaligned Images：图片如果有像素不对齐，拉伸和缩放情况，会被高亮成黄色
* Color Offscreen-Rendered Yellow：离屏渲染的部分会被高亮成黄色
* Color Immediately：设置每帧都更新，通常Core Animation Instruments以每毫秒10次的频率更新图层调试颜色。
* Color OpenGL Fast Path Blue ：对任何直接使用OpenGL绘制的图层进行高亮
* Flash Updated Region ：对重绘的内容高亮成黄色

## 界面卡顿原因
### CPU限制
* 对象的创建，释放，属性调整。这里尤其要提一下属性调整，CALayer的属性调整的时候是会创建隐式动画的，是比较损耗性能的。
* 视图和文本的布局计算，AutoLayout的布局计算都是在主线程上的，所以占用CPU时间也很多 。
* 文本渲染，诸如UILabel和UITextview都是在主线程渲染的
* 图片的解码，这里要提到的是，通常UIImage只有在交给GPU之前的一瞬间，CPU才会对其解码。
### GPU限制
* 视图的混合。比如一个界面十几层的视图叠加到一起，GPU不得不计算每个像素点药显示的像素
* 离屏渲染。视图的Mask，圆角，阴影。
* 半透明，GPU不得不进行数学计算，如果是不透明的，CPU只需要取上层的就可以了
* 浮点数像素

## 界面卡顿优化
### AsyncDisplayKit

### 图文混排引擎
* YYKit
* DTCoreText

### 异步绘制
把复杂的界面，放到后台线程里绘制成一个bitmap，然后再显示。虽然有些延迟，不过换来的却是平滑的界面。

### 图片的解码
建议使用成熟的库，比如SDWebImage等，能够在后台进行图片解码，减少CPU的使用。

### 预加载与缓存
对于复杂的TableView，可以对Cell视图的各个控件的大小，位置后台进行预计算，并且缓存起来。这样保证在heightForRow和cellForRow中不进行大量的计算。

### 尽量使用CALayer
因为Layer是一个轻量级的视图结构，它不接受通知，不接受触摸，不在响应链。所以，相对于UIView来说，它的性能较好。并且CALayer及其子类是可以使用GPU渲染的，能够硬件加速。

### 图层预合成
将两个CALayer的内容合成到一个Bitmap里，然后显示。能够减轻GPU的压力

# Texture(AsyncDisplayKit)
一些影响流畅性的关键点：
* 网络请求，大部分网络请求都应该使用后台线程完成，如果你使用的是 AFNetworking、 SDWebImage 这些开源缓存库，那么切换到后台去请求网络资源的操作都已经默认完成。
* 本地数据读写和计算，当你需要从闪存中读取文件的时候，这些操作都应该使用GCD或者NSThread切换至后台线程中完成。
* 图像的处理，尽量使用合适的UIImage给予UIImageView使用
* Layer 属性的谨慎选择，不合理的 Layer 特效（阴影、圆角）都会使流畅的滑动变成卡顿（非常重要）。
* 少用 UIView.backgroundColor = UIColor.clearColor()，透明的背景会加剧卡顿。
* 文字的渲染，你可能不知道，文字的渲染也是需要开销的。一般来说，文字渲染的开销非常小，甚至不能察觉到。但是，当一个UILabel被赋予大段富文本文字后，开销就会非常大。
* 图像的渲染，一个任何开发者、几乎所有库（包括SDWebImage）都无法解决的问题，图像在UIImageView中的渲染开销，并且图像的渲染只能在主线程中执行。

控件对应关系：
UIKit —— AsyncDisplayKit
UIView —— ASDisplayNode
UILabel —— ASTextNode ：在TextKit上支持所有的富文本特性
UIImageView —— ASImageNode：异步的图像解码
UIButton —— ASButtonNode
UITableView —— ASTableView
UICollectionView —— ASCollectionView
UITableViewCell —— ASCellNode
UICollectionViewCell —— ASCellNode

## Layout Specs（布局规则）
ASInsetLayoutSpec（插入布局规则）
ASOverlayLayoutSpec（覆盖布局规则）
ASBackgroundLayoutSpec（背景布局规则）
ASCenterLayoutSpec（中心布局规则）
ASRatioLayoutSpec（比例布局规则）
ASRelativeLayoutSpec（相对布局规则）
ASStackLayoutSpec（堆叠布局规则）
ASAbsoluteLayoutSpec（绝对布局约束)
ASLayoutSpec（布局规则）
Layout Element Properties（布局元素属性）
ASStackLayoutElement Properties（堆叠布局元素属性）
ASAbsoluteLayoutElement Properties（绝对布局元素属性）
ASLayoutElement Properties（布局元素属性）

[AsyncDisplaykit2.0(Texture)使用「复杂界面流畅性」](https://www.jianshu.com/p/afc69cd9e824)

参考文章：[iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)
[离屏渲染优化详解：实例示范+性能测试](https://www.jianshu.com/p/ca51c9d3575b)
[iOS性能优化：Instrument 调试界面卡顿](https://blog.csdn.net/shaobo8910/article/details/66975785)
[ios核心动画高级技巧](https://zsisme.gitbooks.io/ios-/content/chapter12/instruments.html)
[iOS 开发一定要尝试的 Texture(ASDK)](https://didee.cn/2018/01/29/iOS-ASDK/)
