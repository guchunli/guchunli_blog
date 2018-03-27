---
title: iOS中collectionView的使用以及拖拽重排的实现
date: 2018-03-27 15:10:30
categories: 学习
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


# 使用collectionView实现拖拽重排
## 第一种方法
### 1.给collectionView添加长按手势
```
UILongPressGestureRecognizer *longPressGesture = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressAction:)];
[_collectionView addGestureRecognizer:longPressGesture];
}
```

### 2.实现长按手势的方法
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

### 3.实现移动的数据源方法
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

## 第二种方法
### 1.给collectionViewCell添加长按手势
```
//为每个cell 添加长按手势
UILongPressGestureRecognizer *longPress = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPressCellAction:)];
[cell addGestureRecognizer:longPress];
```

### 2.实现长按手势的方法
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
