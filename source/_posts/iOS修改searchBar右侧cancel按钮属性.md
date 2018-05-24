---
title: iOS修改searchBar右侧cancel按钮属性
date: 2018-05-03 09:50:25
categories: 学习
tags: [searchBar,cancel]
toc:
---

1.遍历searchBar子视图
```
- (void)searchBarTextDidBeginEditing:(UISearchBar *)searchBar {
searchBar.showsCancelButton = YES;
for (UIView *view in [_searchBar.subviews[0] subviews]) {
if ([view isKindOfClass:[UIButton class]]) {
UIButton *cancel = (UIButton *)view;
[cancel setTitle:@"确定" forState:UIControlStateNormal];
[cancel setTitleColor:WYContentColor forState:UIControlStateNormal];
}
}
}
- (void)searchBarTextDidEndEditing:(UISearchBar *)searchBar
{
searchBar.showsCancelButton = NO;
}
```

2.setValue: forkey:
```
//这种写法解决了上面那种写法在弹出取消按钮时不自然的问题。
        [_searchBar setValue:@"确定" forKey: @"_cancelButtonText"];
        [_searchBar setValue:@"确定" forKey: @"_cancelButtonText"];
```

3.iOS8也可以这样写
```
        [[UIBarButtonItem appearanceWhenContainedIn:[UISearchBar class], nil] setTitle:@"确定"];
        [[UIBarButtonItem appearanceWhenContainedIn:[UISearchBar class], nil] setTintColor:WYContentColor];
```
4.iOS9以后可以这样写
```
        [UIBarButtonItem appearanceWhenContainedInInstancesOfClasses:@[[UISearchBar class]]].title = @"确定";
        [UIBarButtonItem appearanceWhenContainedInInstancesOfClasses:@[[UISearchBar class]]].tintColor = WYContentColor;
```
