---
title: iOS中UICollectionView的使用
date: 2018-03-27 15:10:30
categories: 笔记
tags: [collectionView,拖拽,重排]
toc:
---

# UICollectionView的使用
## UICollectionViewDataSource
<!--more-->
```
//设置分区数
- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView
{
    return 1;
}
//设置每个分区的item个数
- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
return self.data.count;
}
//设置返回每个item的属性
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
RACollectionViewCell *cell = [self.collectionView dequeueReusableCellWithReuseIdentifier:cellID forIndexPath:indexPath];
[cell.imageView removeFromSuperview];
cell.imageView.frame = cell.bounds;
cell.img = self.data[indexPath.row];
//    cell.imageView.image = self.data[indexPath.row];
[cell.contentView addSubview:cell.imageView];

return cell;
}

//对头视图或者尾视图进行设置
// The view that is returned must be retrieved from a call to -dequeueReusableSupplementaryViewOfKind:withReuseIdentifier:forIndexPath:
//- (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath{
//
//}
```

### 移动
```
//设置某个item是否可以被移动，返回NO则不能移动
- (BOOL)collectionView:(UICollectionView *)collectionView canMoveItemAtIndexPath:(NSIndexPath *)indexPath{
return YES;
}
//移动item的时候，会调用这个方法
- (void)collectionView:(UICollectionView *)collectionView moveItemAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath*)destinationIndexPath{

UIImage *img = self.data[sourceIndexPath.row];
[self.data removeObjectAtIndex:sourceIndexPath.row];
[self.data insertObject:img atIndex:destinationIndexPath.row];
}
```

## UICollectionViewDelegate
```
//是否允许某个Item的高亮，返回NO，则不能进入高亮状态
- (BOOL)collectionView:(UICollectionView *)collectionView shouldHighlightItemAtIndexPath:(NSIndexPath *)indexPath;

//当item高亮时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didHighlightItemAtIndexPath:(NSIndexPath *)indexPath;

//结束高亮状态时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didUnhighlightItemAtIndexPath:(NSIndexPath *)indexPath;

//是否可以选中某个Item，返回NO，则不能选中
- (BOOL)collectionView:(UICollectionView *)collectionView shouldSelectItemAtIndexPath:(NSIndexPath *)indexPath;

//是否可以取消选中某个Item
- (BOOL)collectionView:(UICollectionView *)collectionView shouldDeselectItemAtIndexPath:(NSIndexPath *)indexPath;

//已经选中某个item时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath;

//取消选中某个Item时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didDeselectItemAtIndexPath:(NSIndexPath *)indexPath;

//将要加载某个Item时调用的方法
- (void)collectionView:(UICollectionView *)collectionView willDisplayCell:(UICollectionViewCell *)cell forItemAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(8_0);

//将要加载头尾视图时调用的方法
- (void)collectionView:(UICollectionView *)collectionView willDisplaySupplementaryView:(UICollectionReusableView *)view forElementKind:(NSString *)elementKind atIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(8_0);

//已经展示某个Item时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didEndDisplayingCell:(UICollectionViewCell *)cell forItemAtIndexPath:(NSIndexPath *)indexPath;

//已经展示某个头尾视图时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didEndDisplayingSupplementaryView:(UICollectionReusableView *)view forElementOfKind:(NSString *)elementKind atIndexPath:(NSIndexPath *)indexPath;

//设置是否展示长按菜单
- (BOOL)collectionView:(UICollectionView *)collectionView shouldShowMenuForItemAtIndexPath:(NSIndexPath *)indexPath;

//设置要展示的菜单选项
- (BOOL)collectionView:(UICollectionView *)collectionView canPerformAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(nullable id)sender;

//实现点击菜单按钮后的触发方法,通过测试，只有copy，cut和paste三个方法可以使用
- (void)collectionView:(UICollectionView *)collectionView performAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(nullable id)sender;

//collectionView进行重新布局时调用的方法
- (nonnull UICollectionViewTransitionLayout *)collectionView:(UICollectionView *)collectionView transitionLayoutForOldLayout:(UICollectionViewLayout *)fromLayout newLayout:(UICollectionViewLayout *)toLayout;
```

## UICollectionViewDelegateFlowLayout<UICollectionViewDelegate>
```
//设置item的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath{
CGFloat itemW = (self.view.frame.size.width-(ItemNumber-1)*Margin-Inset*2-1)/(CGFloat)ItemNumber;
return CGSizeMake(itemW, itemW);
}
//四周的边距
- (UIEdgeInsets)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout insetForSectionAtIndex:(NSInteger)section{
return UIEdgeInsetsMake(Inset, Inset, Inset, Inset);
}
//设置垂直间距,默认的垂直和水平间距都是10
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumLineSpacingForSectionAtIndex:(NSInteger)section{
return Margin;
}
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumInteritemSpacingForSectionAtIndex:(NSInteger)section{
return Margin;
}
/*
//设置header的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForHeaderInSection:(NSInteger)section{

}
//设置footer的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForFooterInSection:(NSInteger)section{

}
*/
```

# 自定义UICollectionViewFlowLayout
## 在UICollectionViewFlowLayout类中完成四步：
（1）重写prepareLayout方法
- 作用：在这个方法中做一些初始化操作，进行基本的布局，不能在init中布局，因为设置collectionView尺寸是在viewDidLoad中，而init在它之前调用，获得的collectionView的尺寸是空的
- 注意：一定要调用[super prepareLayout]

（2）重写layoutAttributesForElementsInRect:方法
- 作用：当collectionView的显示范围发生改变的时候，让其内部重新布局
- 这个方法的返回值是个数组
- 这个数组中存放的都是UICollectionViewLayoutAttributes对象
- UICollectionViewLayoutAttributes对象决定了cell的排布方式（frame等）

（3）重写shouldInvalidateLayoutForBoundsChange:方法
- 作用：如果返回YES，那么collectionView显示的范围发生改变时，就会重新刷新布局
- 一旦重新刷新布局，就会按顺序调用下面的方法：
- prepareLayout
- layoutAttributesForElementsInRect:

（4）重写targetContentOffsetForProposedContentOffset:withScrollingVelocity:方法
- 作用：返回值决定了collectionView停止滚动时最终的偏移量（contentOffset）
- 参数：
- proposedContentOffset：原本情况下，collectionView停止滚动时最终的偏移量
- velocity：滚动速率，通过这个参数可以了解滚动的方向

## 例如实现一个相册效果，照片滚动到中间放大：
（1）prepareLayout：cell在最左面的时候是在正中间
（2）layoutAttributesForElementsInRect：让cell滚动起来
（3）shouldInvalidateLayoutForBoundsChange：让cell在左右滑动的时候，尺寸放大或缩小
（4）targetContentOffsetForProposedContentOffset:withScrollingVelocity：让最接近中心的cell在停在正中央
```
- (instancetype)init
{
    if (self = [super init]) {

    }
    return self;
}
/**
6  * 用来做布局的初始化操作（不建议在init方法中进行布局的初始化操作）
7  */
8 - (void)prepareLayout
9 {
10     [super prepareLayout];
11     //水平滚动
12     self.scrollDirection = UICollectionViewScrollDirectionHorizontal;
13
14     //
15     CGFloat margin = (self.collectionView.frame.size.width - self.itemSize.width) / 2;
16     self.collectionView.contentInset = UIEdgeInsetsMake(0, margin, 0, margin);
17 }
18
19 /**
20  * 当collectionView的显示范围发生改变的时候，是否需要重新刷新布局
21  * 一旦重新刷新布局，就会重新调用下面的方法：
22  * 1.prepareLayout
23  * 2.layoutAttributesForElementsInRect:方法
24  */
25 - (BOOL)shouldInvalidateLayoutForBoundsChange:(CGRect)newBounds
26 {
27     return YES;
28 }
29
30
31 /**
32  * 这个方法的返回值是一个数组（数组里面存放着rect范围内所有元素的布局属性）
33  * 这个方法的返回值决定了rect范围内所有元素的排布（frame）
34  */
35 //需要在viewController中使用上ZWLineLayout这个类后才能重写这个方法！！
36 - (NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect
37 {
38     //让父类布局好样式
39     NSArray *arr = [super layoutAttributesForElementsInRect:rect];
40     //计算出collectionView的中心的位置
41     CGFloat ceterX = self.collectionView.contentOffset.x + self.collectionView.frame.size.width * 0.5;
42     /**
43      * 1.一个cell对应一个UICollectionViewLayoutAttributes对象
44      * 2.UICollectionViewLayoutAttributes对象决定了cell的frame
45      */
46     for (UICollectionViewLayoutAttributes *attributes in arr) {
47         //cell的中心点距离collectionView的中心点的距离，注意ABS()表示绝对值
48         CGFloat delta = ABS(attributes.center.x - ceterX);
49         //设置缩放比例
50         CGFloat scale = 1.1 - delta / self.collectionView.frame.size.width;
51         //设置cell滚动时候缩放的比例
52         attributes.transform = CGAffineTransformMakeScale(scale, scale);
53     }
54
55     return arr;
56 }
57
58 /**
59  * 这个方法的返回值，就决定了collectionView停止滚动时的偏移量
60  */
61 - (CGPoint)targetContentOffsetForProposedContentOffset:(CGPoint)proposedContentOffset withScrollingVelocity:(CGPoint)velocity
62 {
63     // 计算出最终显示的矩形框
64     CGRect rect;
65     rect.origin.y = 0;
66     rect.origin.x = proposedContentOffset.x;
67     rect.size = self.collectionView.frame.size;
68
69     //获得super已经计算好的布局的属性
70     NSArray *arr = [super layoutAttributesForElementsInRect:rect];
71
72     //计算collectionView最中心点的x值
73     CGFloat centerX = proposedContentOffset.x + self.collectionView.frame.size.width * 0.5;
74
75     CGFloat minDelta = MAXFLOAT;
76     for (UICollectionViewLayoutAttributes *attrs in arr) {
77         if (ABS(minDelta) > ABS(attrs.center.x - centerX)) {
78             minDelta = attrs.center.x - centerX;
79         }
80     }
81     proposedContentOffset.x += minDelta;
82     return proposedContentOffset;
83 }
```

## 使用collectionView实现拖拽重排
### 第一种方法（从iOS9开始支持）
1.给collectionView添加长按手势
```
UILongPressGestureRecognizer *longPressGesture = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressAction:)];
[_collectionView addGestureRecognizer:longPressGesture];
}
```

2.实现长按手势的方法
```
- (void)longPressAction:(UILongPressGestureRecognizer *)longPress {
//获取此次点击的坐标，根据坐标获取cell对应的indexPath
CGPoint point = [longPress locationInView:_collectionView];
NSIndexPath *indexPath = [self.collectionView indexPathForItemAtPoint:point];
//根据长按手势的状态进行处理。
switch (longPress.state) {
case UIGestureRecognizerStateBegan:
//当没有点击到cell的时候不进行处理
if (!indexPath) {
break;
}
//开始移动
[_collectionView beginInteractiveMovementForItemAtIndexPath:indexPath];
break;
case UIGestureRecognizerStateChanged:
//移动过程中更新位置坐标
[_collectionView updateInteractiveMovementTargetPosition:point];
break;
case UIGestureRecognizerStateEnded:
//停止移动调用此方法
[_collectionView endInteractiveMovement];
break;
default:
//取消移动
[_collectionView cancelInteractiveMovement];
break;
}
}
```

3.实现移动的数据源方法
```
#pragma mark - 移动
//设置某个item是否可以被移动，返回NO则不能移动
- (BOOL)collectionView:(UICollectionView *)collectionView canMoveItemAtIndexPath:(NSIndexPath *)indexPath{
return YES;
}
//移动item的时候，会调用这个方法
- (void)collectionView:(UICollectionView *)collectionView moveItemAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath*)destinationIndexPath{

UIImage *img = self.data[sourceIndexPath.row];
[self.data removeObjectAtIndex:sourceIndexPath.row];
[self.data insertObject:img atIndex:destinationIndexPath.row];
}
```

### 第二种方法（从iOS8开始支持）
1.给collectionViewCell添加长按手势
```
//为每个cell 添加长按手势
UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressCellAction:)];
[cell addGestureRecognizer:longPress];
```

2.实现长按手势的方法
```
- (void)longPressCellAction:(UILongPressGestureRecognizer *)longPress {

RACollectionViewCell *cell = (RACollectionViewCell *)longPress.view;
NSIndexPath *cellIndexpath = [_collectionView indexPathForCell:cell];

[_collectionView bringSubviewToFront:cell];

_isChange = NO;

switch (longPress.state) {
case UIGestureRecognizerStateBegan: {
[self.cellAttributesArray removeAllObjects];
for (int i = 0; i < self.data.count; i++) {
[self.cellAttributesArray addObject:[_collectionView layoutAttributesForItemAtIndexPath:[NSIndexPath indexPathForRow:i inSection:0]]];
}

}

break;

case UIGestureRecognizerStateChanged: {

cell.center = [longPress locationInView:_collectionView];

for (UICollectionViewLayoutAttributes *attributes in self.cellAttributesArray) {
if (CGRectContainsPoint(attributes.frame, cell.center) && cellIndexpath != attributes.indexPath) {
_isChange = YES;
UIImage *img = self.data[cellIndexpath.row];
[self.data removeObjectAtIndex:cellIndexpath.row];
[self.data insertObject:img atIndex:attributes.indexPath.row];
[self.collectionView moveItemAtIndexPath:cellIndexpath toIndexPath:attributes.indexPath];
}
}

}

break;

case UIGestureRecognizerStateEnded: {

if (!_isChange) {
cell.center = [_collectionView layoutAttributesForItemAtIndexPath:cellIndexpath].center;
}
}

break;

default:
break;
}
}
```

完整demo：
```
#import "BaseViewController.h"
#import "RACollectionViewCell.h"
#import "WYAlertController.h"
#define ItemNumber  3
#define Margin  5.f
#define Inset   15.f

@interface BaseViewController ()<UICollectionViewDelegateFlowLayout,UICollectionViewDataSource>

@property (nonatomic, strong) UICollectionView *collectionView;
@property (nonatomic, strong) NSMutableArray *data;
//使用数组将collectionView每个cell的 UICollectionViewLayoutAttributes 存储起来
@property (nonatomic, strong) NSMutableArray *cellAttributesArray;
@property (nonatomic, assign) BOOL isChange;

@end

@implementation BaseViewController

static NSString *cellID = @"cellID";
- (void)viewDidLoad {
[super viewDidLoad];

[self.view addSubview:self.collectionView];
[self setupPhotosArray];
[self.collectionView reloadData];
}

- (void)setupPhotosArray
{
for (int i = 1; i <= 20; i++) {
UIImage *photo = [UIImage imageNamed:[NSString stringWithFormat:@"%d.jpg",i]];
[self.data addObject:photo];
}
}

- (NSMutableArray *)data {
if(_data == nil) {
_data = [[NSMutableArray alloc] init];
}
return _data;
}

-(NSMutableArray *)cellAttributesArray{
if (!_cellAttributesArray) {
_cellAttributesArray = [NSMutableArray arrayWithCapacity:3];
}
return _cellAttributesArray;
}

- (UICollectionView *)collectionView {
if (_collectionView == nil) {
//设置布局属性
UICollectionViewFlowLayout *flow = [[UICollectionViewFlowLayout alloc] init];
//添加collectionView
_collectionView = [[UICollectionView alloc] initWithFrame:CGRectMake(0, 20, self.view.frame.size.width, self.view.frame.size.height-20) collectionViewLayout:flow];
_collectionView.backgroundColor = [UIColor whiteColor];
//设置代理
_collectionView.dataSource = self;
_collectionView.delegate = self;
//注册
[_collectionView registerClass:[RACollectionViewCell class] forCellWithReuseIdentifier:cellID];

//        UILongPressGestureRecognizer *longPressGesture = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressAction:)];
//        [_collectionView addGestureRecognizer:longPressGesture];
}
return _collectionView;
}

- (void)longPressAction:(UILongPressGestureRecognizer *)longPress {
//获取此次点击的坐标，根据坐标获取cell对应的indexPath
CGPoint point = [longPress locationInView:_collectionView];
NSIndexPath *indexPath = [self.collectionView indexPathForItemAtPoint:point];
//根据长按手势的状态进行处理。
switch (longPress.state) {
case UIGestureRecognizerStateBegan:
//当没有点击到cell的时候不进行处理
if (!indexPath) {
break;
}
//开始移动
[_collectionView beginInteractiveMovementForItemAtIndexPath:indexPath];
break;
case UIGestureRecognizerStateChanged:
//移动过程中更新位置坐标
[_collectionView updateInteractiveMovementTargetPosition:point];
break;
case UIGestureRecognizerStateEnded:
//停止移动调用此方法
[_collectionView endInteractiveMovement];
break;
default:
//取消移动
[_collectionView cancelInteractiveMovement];
break;
}
}

#pragma mark - UICollectionViewDataSource
//设置分区数
//- (NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView
//{
//    return ItemNumber;
//}
//设置每个分区的item个数
- (NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section
{
return self.data.count;
}
//设置返回每个item的属性
- (UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath
{
RACollectionViewCell *cell = [self.collectionView dequeueReusableCellWithReuseIdentifier:cellID forIndexPath:indexPath];
[cell.imageView removeFromSuperview];
cell.imageView.frame = cell.bounds;
cell.img = self.data[indexPath.row];
//    cell.imageView.image = self.data[indexPath.row];
[cell.contentView addSubview:cell.imageView];

//为每个cell 添加长按手势
UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressCellAction:)];
[cell addGestureRecognizer:longPress];

return cell;
}


- (void)longPressCellAction:(UILongPressGestureRecognizer *)longPress {

RACollectionViewCell *cell = (RACollectionViewCell *)longPress.view;
NSIndexPath *cellIndexpath = [_collectionView indexPathForCell:cell];

[_collectionView bringSubviewToFront:cell];

_isChange = NO;

switch (longPress.state) {
case UIGestureRecognizerStateBegan: {
[self.cellAttributesArray removeAllObjects];
for (int i = 0; i < self.data.count; i++) {
[self.cellAttributesArray addObject:[_collectionView layoutAttributesForItemAtIndexPath:[NSIndexPath indexPathForRow:i inSection:0]]];
}

}

break;

case UIGestureRecognizerStateChanged: {

cell.center = [longPress locationInView:_collectionView];

for (UICollectionViewLayoutAttributes *attributes in self.cellAttributesArray) {
if (CGRectContainsPoint(attributes.frame, cell.center) && cellIndexpath != attributes.indexPath) {
_isChange = YES;
UIImage *img = self.data[cellIndexpath.row];
[self.data removeObjectAtIndex:cellIndexpath.row];
[self.data insertObject:img atIndex:attributes.indexPath.row];
[self.collectionView moveItemAtIndexPath:cellIndexpath toIndexPath:attributes.indexPath];
}
}

}

break;

case UIGestureRecognizerStateEnded: {

if (!_isChange) {
cell.center = [_collectionView layoutAttributesForItemAtIndexPath:cellIndexpath].center;
}
}

break;

default:
break;
}

}

//对头视图或者尾视图进行设置
// The view that is returned must be retrieved from a call to -dequeueReusableSupplementaryViewOfKind:withReuseIdentifier:forIndexPath:
//- (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath{
//
//}

#pragma mark - 移动
//设置某个item是否可以被移动，返回NO则不能移动
//- (BOOL)collectionView:(UICollectionView *)collectionView canMoveItemAtIndexPath:(NSIndexPath *)indexPath{
//    return YES;
//}
////移动item的时候，会调用这个方法
//- (void)collectionView:(UICollectionView *)collectionView moveItemAtIndexPath:(NSIndexPath *)sourceIndexPath toIndexPath:(NSIndexPath*)destinationIndexPath{
//
//    UIImage *img = self.data[sourceIndexPath.row];
//    [self.data removeObjectAtIndex:sourceIndexPath.row];
//    [self.data insertObject:img atIndex:destinationIndexPath.row];
//}

/*
/// Returns a list of index titles to display in the index view (e.g. ["A", "B", "C" ... "Z", "#"])
- (nullable NSArray<NSString *> *)indexTitlesForCollectionView:(UICollectionView *)collectionView API_AVAILABLE(tvos(10.2));

/// Returns the index path that corresponds to the given title / index. (e.g. "B",1)
/// Return an index path with a single index to indicate an entire section, instead of a specific item.
- (NSIndexPath *)collectionView:(UICollectionView *)collectionView indexPathForIndexTitle:(NSString *)title atIndex:(NSInteger)index API_AVAILABLE(tvos(10.2));
*/

#pragma mark - UICollectionViewDelegate
/*
//是否允许某个Item的高亮，返回NO，则不能进入高亮状态
- (BOOL)collectionView:(UICollectionView *)collectionView shouldHighlightItemAtIndexPath:(NSIndexPath *)indexPath;

//当item高亮时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didHighlightItemAtIndexPath:(NSIndexPath *)indexPath;

//结束高亮状态时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didUnhighlightItemAtIndexPath:(NSIndexPath *)indexPath;

//是否可以选中某个Item，返回NO，则不能选中
- (BOOL)collectionView:(UICollectionView *)collectionView shouldSelectItemAtIndexPath:(NSIndexPath *)indexPath;

//是否可以取消选中某个Item
- (BOOL)collectionView:(UICollectionView *)collectionView shouldDeselectItemAtIndexPath:(NSIndexPath *)indexPath;

//已经选中某个item时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath;

//取消选中某个Item时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didDeselectItemAtIndexPath:(NSIndexPath *)indexPath;

//将要加载某个Item时调用的方法
- (void)collectionView:(UICollectionView *)collectionView willDisplayCell:(UICollectionViewCell *)cell forItemAtIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(8_0);

//将要加载头尾视图时调用的方法
- (void)collectionView:(UICollectionView *)collectionView willDisplaySupplementaryView:(UICollectionReusableView *)view forElementKind:(NSString *)elementKind atIndexPath:(NSIndexPath *)indexPath NS_AVAILABLE_IOS(8_0);

//已经展示某个Item时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didEndDisplayingCell:(UICollectionViewCell *)cell forItemAtIndexPath:(NSIndexPath *)indexPath;

//已经展示某个头尾视图时触发的方法
- (void)collectionView:(UICollectionView *)collectionView didEndDisplayingSupplementaryView:(UICollectionReusableView *)view forElementOfKind:(NSString *)elementKind atIndexPath:(NSIndexPath *)indexPath;

//设置是否展示长按菜单
- (BOOL)collectionView:(UICollectionView *)collectionView shouldShowMenuForItemAtIndexPath:(NSIndexPath *)indexPath;

//设置要展示的菜单选项
- (BOOL)collectionView:(UICollectionView *)collectionView canPerformAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(nullable id)sender;

//实现点击菜单按钮后的触发方法,通过测试，只有copy，cut和paste三个方法可以使用
- (void)collectionView:(UICollectionView *)collectionView performAction:(SEL)action forItemAtIndexPath:(NSIndexPath *)indexPath withSender:(nullable id)sender;

//collectionView进行重新布局时调用的方法
- (nonnull UICollectionViewTransitionLayout *)collectionView:(UICollectionView *)collectionView transitionLayoutForOldLayout:(UICollectionViewLayout *)fromLayout newLayout:(UICollectionViewLayout *)toLayout;
*/

//设置选中
//点击移除图片
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath
{
WYAlertController *alert = [WYAlertController initWYAlertControlWithTitle:nil message:@"移除图片" Style:UIAlertControllerStyleActionSheet titleArr:@[@"移除图片",@"取消"] alerAction:^(NSInteger index) {
if (index == 0) {
[self removeCellWithIndexPath:indexPath];
}
}];
[alert showWYAler];
}

- (void)removeCellWithIndexPath:(NSIndexPath *)indexPath{

//在执行完performBatchUpdates操作之后，collection view会自动reloadData
//    [self.collectionView performBatchUpdates:^{
//        [self.collectionView deleteItemsAtIndexPaths:@[indexPath]];
[self.data removeObjectAtIndex:indexPath.item];
//    } completion:^(BOOL finished) {
[self.collectionView reloadData];
//    }];
}

#pragma mark - UICollectionViewDelegateFlowLayout<UICollectionViewDelegate>
//设置item的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout sizeForItemAtIndexPath:(NSIndexPath *)indexPath{
CGFloat itemW = (self.view.frame.size.width-(ItemNumber-1)*Margin)/(CGFloat)ItemNumber;
return CGSizeMake(itemW, itemW);
}
//四周的边距
- (UIEdgeInsets)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout insetForSectionAtIndex:(NSInteger)section{
return UIEdgeInsetsMake(Inset, Inset, Inset, Inset);
}
//设置垂直间距,默认的垂直和水平间距都是10
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumLineSpacingForSectionAtIndex:(NSInteger)section{
return Margin;
}
- (CGFloat)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout minimumInteritemSpacingForSectionAtIndex:(NSInteger)section{
return Margin;
}
/*
//设置header的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForHeaderInSection:(NSInteger)section{

}
//设置footer的大小
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForFooterInSection:(NSInteger)section{

}
*/
```

自定义cell：
```
@interface RACollectionViewCell : UICollectionViewCell

@property (nonatomic, strong) UIImageView *imageView;

@property (nonatomic,strong)UIImage *img;

@end


#import "RACollectionViewCell.h"

@implementation RACollectionViewCell

- (instancetype)initWithFrame:(CGRect)frame
{
self = [super initWithFrame:frame];
if (self) {
self.backgroundColor = [UIColor orangeColor];

}
return self;
}

-(UIImageView *)imageView{
if (!_imageView) {
_imageView = [[UIImageView alloc] init];
//        _imageView.contentMode = UIViewContentModeScaleAspectFill;
_imageView.autoresizingMask = UIViewAutoresizingFlexibleHeight | UIViewAutoresizingFlexibleWidth;
}
return _imageView;
}

- (void)setImg:(UIImage *)img {
_img  = img;

self.imageView.image = img;
}

- (void)setHighlighted:(BOOL)highlighted
{
[super setHighlighted:highlighted];
if (highlighted) {
_imageView.alpha = .7f;
}else {
_imageView.alpha = 1.f;
}
}

@end
```

## 瀑布流实现
```
@interface TestLayout : UICollectionViewFlowLayout

@property(nonatomic,assign)NSInteger itemCount;

@end



@interface TestLayout ()
{
NSMutableArray * _attributeArray;
}
@end

@implementation TestLayout

-(void)prepareLayout{

_attributeArray = [NSMutableArray array];
[super prepareLayout];

_itemCount = (int)[self.collectionView numberOfItemsInSection:0];

float WIDTH = ([UIScreen mainScreen].bounds.size.width-self.sectionInset.left-self.sectionInset.right-self.minimumInteritemSpacing)/2;
CGFloat colHight[2] = {self.sectionInset.top,self.sectionInset.bottom};
for (int i=0; i<_itemCount; i++) {

NSIndexPath * index = [NSIndexPath indexPathForItem:i inSection:0];
UICollectionViewLayoutAttributes * attribute =  [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:index];
CGFloat height = arc4random()%150+40;
int row = 0;
if (colHight[0]<colHight[1]) {
colHight[0] = colHight[0]+height+self.minimumLineSpacing;
row = 0;
}else{
colHight[1] = colHight[1]+height+self.minimumLineSpacing;
row = 1;
}
attribute.frame = CGRectMake(self.sectionInset.left+(self.minimumInteritemSpacing+WIDTH)*row, colHight[row]-height-self.minimumLineSpacing, WIDTH, height);
[_attributeArray addObject:attribute];

}
if (colHight[0]>colHight[1]) {
self.itemSize = CGSizeMake(WIDTH, (colHight[0]-self.sectionInset.top)*2/_itemCount-self.minimumLineSpacing);
}else{
self.itemSize = CGSizeMake(WIDTH, (colHight[1]-self.sectionInset.top)*2/_itemCount-self.minimumLineSpacing);
}

}
-(NSArray<UICollectionViewLayoutAttributes *> *)layoutAttributesForElementsInRect:(CGRect)rect{
return _attributeArray;
}
```

## collectionCell根据内容自动适应宽度
1.设置layout.estimatedItemSize
2.在自定义cell中实现preferredLayoutAttributesFittingAttributes
```
#pragma mark — 实现自适应文字宽度的关键步骤:item的layoutAttributes
-(UICollectionViewLayoutAttributes *)preferredLayoutAttributesFittingAttributes:(UICollectionViewLayoutAttributes *)layoutAttributes{

    UICollectionViewLayoutAttributes *attributes = [super preferredLayoutAttributesFittingAttributes:layoutAttributes];
    //    CGRect maxBounds= CGRectMake(0, 0, CGFLOAT_MAX, self.textLabel.frame.size.height);
    //    attributes.frame = [self.textLabel textRectForBounds:maxBounds limitedToNumberOfLines:self.textLabel.numberOfLines];
    attributes.frame = [self.textLabel.text boundingRectWithSize:CGSizeMake(CGFLOAT_MAX, self.textLabel.frame.size.height) options:NSStringDrawingUsesLineFragmentOrigin attributes:@{NSFontAttributeName:self.textLabel.font} context:nil];
    return attributes;
}
```

# UITableView自动调整
UITableViewDelegate新增了三个方法来满足用户设定Cell、Header和Footer预计高度的方法:
```
- tableView:estimatedHeightForRowAtIndexPath:
- tableView:estimatedHeightForHeaderInSection:
- tableView:estimatedHeightForFooterInSection:
```
当然对应这三个方法UITableView也有estimatedRowHeight、estimatedSectionHeaderHeight和estimatedSectionFooterHeight三个属性，局限性在于只能统一定义所有行和节的高度。

参考：[两列瀑布流](https://github.com/Flying-Einstein/CollectionViewTest)
[三列瀑布流](https://github.com/codingZero/XRWaterfallLayout)
