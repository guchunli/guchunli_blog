---
title: iOS提高界面流畅度的技巧
date: 2017-12-19 19:10:30
categories: 笔记
tags: [流畅]
toc:
---

## CPU资源消耗原因和解决方案
### 对象创建
* 尽量用轻量的对象代替重量的对象
* 使用storyboard创建对象比通过代码消耗资源多
<!--more-->
* 尽量推迟对象创建的时间
* 能复用的对象尽量使用复用

### 对象调整
* 尽量避免调整视图层次，添加或移除视图

### 布局计算
* 视图布局的计算是APP中最常见消耗CPU的地方，尽量在`后台线程`计算好视图布局并对视图布局进行`缓存`，尽量`提前`算好布局，在需要时一次性调整好对应属性，而不要多次，频繁的计算、调整这些属性

### autolayout
* autolayout对于复杂视图来说常常会产生严重的性能问题，尽量使用category来快捷地获取视图的width,height等属性。

### 文本计算、渲染
* 可以参考UILabel 内部的实现方式：用 [NSAttributedString boundingRectWithSize:options:context:] 来计算文本宽高，用 -[NSAttributedString drawWithRect:options:context:] 来绘制文本，注意需要放到后台线程进行以避免阻塞主线程。
* 用 TextKit 或最底层的 CoreText 对文本异步绘制。CoreText 对象占用内存较少，可以缓存下来以备稍后多次渲染。屏幕上能看到的所有文本内容控件，在底层都是通过 CoreText 排版、绘制为 Bitmap 显示的。

## GPU资源消耗原因和解决方案
* GPU的作用：接收提交的纹理和顶点描绘，应用变换，混合和渲染，并且输出到屏幕上。

### 纹理的渲染
* 尽量减少在短时间内大量图片的显示，尽可能多张图片合成为一张进行显示。

### 视图的混合
* 尽量减少视图数量和层次

### 图形的生成
* 预渲染：避免使用CALayer的边框，圆角，阴影，遮罩等属性，为了避免离屏渲染，尽量在后台线程预先绘制好对应内容。

## AsyncDisplayKit(ASDK)

## FDTemplateLayoutCell
* 使用 TableView 的预估高度的功能，并把每个 Cell 高度缓存下来。

## 常用技巧
### 预渲染
* 在后台线程将头像预先渲染为圆形并单独保存到一个 ImageCache 中去。

### 异步绘制
* 当 TableView 快速滑动时，会有大量异步绘制任务提交到后台线程去执行。可参考`YYAsyncLayer`

### 全局并发控制
* YYDispatchQueuePool

### 更高效的异步图片加载

## product->profile


# 提高列表滚动流畅度
## 避免重复多次计算 cell 行高
* 获取都数据
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



参考文章：[iOS 保持界面流畅的技巧](https://blog.ibireme.com/2015/11/12/smooth_user_interfaces_for_ios/)
