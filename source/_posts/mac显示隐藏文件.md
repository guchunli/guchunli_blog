---
title: mac显示隐藏文件
date: 2017-03-21 18:37:39
categories: 学习
tags: [技巧,隐藏文件]
---

1.显示隐藏文件
<!--more-->
```
defaults write com.apple.finder AppleShowAllFiles Yes && killall Finder 
```

2.不显示隐藏文件
```
defaults write com.apple.finder AppleShowAllFiles No && killall Finder 
```

3.删除文件夹下的所有 .git 文件
```
find . -name ".git" | xargs rm -Rf　　
```
