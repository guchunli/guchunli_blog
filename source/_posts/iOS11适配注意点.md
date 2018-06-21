---
title: iOS11适配注意点
date: 2017-10-16 09:59:02
categories: 笔记
tags: [iOS11]
toc:
---

## 1.UIBarItem

### landscapeImagePhone：横屏时，长按item，item放大显示在hud上

<!--more-->
## 2.navigationItem

### largeTitleDisplayMode：控制大标题的显示

## 3.UISearchController

### navigationItem.searchController  //将UISearchController集成到Navigation
### navigationItem.hidesSearchBarWhenScrolling //决定滑动的时候是否隐藏搜索框；iOS 11 新增属性

## 4.automaticallyAdjustsScrollViewInsets

在iOS11之前，如果想要scrollView不偏移64p，则需设置automaticallyAdjustsScrollViewInsets=NO，但是这个属性在iOS11直接被遗弃了，解决方法：
```
if (@available(iOS 11.0, *)) {
self.tableView.contentInsetAdjustmentBehavior = UIScrollViewContentInsetAdjustmentNever;
} else {
self.automaticallyAdjustsScrollViewInsets = NO;
}
```

## 5.UIScrollView & UITableVIew iOS11开启了Self-Sizing行高估算机制
* 解决方法一 ：添加实现View的代理方法
```
- (UIView *)tableView:(UITableView *)tableView viewForFooterInSection:(NSInteger)section {
return nil;
}
- (UIView *)tableView:(UITableView *)tableView viewForHeaderInSection:(NSInteger)section {
return nil;
}
```

* 解决方法二：添加以下代码关闭估算行高：
```
self.tableView.estimatedRowHeight = 0;
self.tableView.estimatedSectionHeaderHeight = 0;
self.tableView.estimatedSectionFooterHeight = 0;
```

### 如果使用了Masonry 进行布局，需要适配safeArea
```
if ([UIDevice currentDevice].systemVersion.floatValue >= 11.0) {
make.edges.equalTo(self.view.safeAreaInsets);
} else {
make.edges.equalTo(self.view);
}
```

## 待补充

