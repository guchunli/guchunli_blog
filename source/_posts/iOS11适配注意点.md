---
title: iOS11适配注意点
date: 2017-10-16 09:59:02
categories: 学习
tags: [iOS11]
toc:
---

## UIBarItem
### landscapeImagePhone：横屏时，长按item，item放大显示在hud上

<!--more-->
## navigationItem
### largeTitleDisplayMode：控制大标题的显示

## UISearchController
### navigationItem.searchController  //将UISearchController集成到Navigation
### navigationItem.hidesSearchBarWhenScrolling //决定滑动的时候是否隐藏搜索框；iOS 11 新增属性

## UIScrollView & UITableVIew
在iOS 11中默认启用Self-Sizing，可以通过以下方式关闭：
```
self.tableView.estimatedRowHeight = 0;
self.tableView.estimatedSectionHeaderHeight = 0;
self.tableView.estimatedSectionFooterHeight = 0;
```

//全局适配
```
if (@available(iOS 11.0, *)){
//[[UIScrollView appearance] setContentInsetAdjustmentBehavior:UIScrollViewContentInsetAdjustmentNever];
[UITableView appearance].estimatedRowHeight = 0;
[UITableView appearance].estimatedSectionHeaderHeight = 0;
[UITableView appearance].estimatedSectionFooterHeight = 0;
}
```
