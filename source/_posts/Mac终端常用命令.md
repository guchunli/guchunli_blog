---
title: Mac终端常用命令
date: 2018-05-25 11:35:36
categories: 学习
tags: [mac,终端,命令]
toc:
---

## Unix命令基础
（1）创建文件夹：mkdir
（2）跳到指定路径：cd
（3）查看当前完整路径：pwd
（4）查看当前路径下的所有文件及文件夹：ls
（5）cat：可以显示文件内容，但是不能编辑
（6）touch：如果文件存在，使用touch指令可更改这个文件或目录的日期时间，包括存取时间和更改时间； 
如果文件不存在，touch指令会在当前目录下新建一个空白文件
（7）vi/vim：打开文件后可以编辑
（8）echo：输出
（9）删除文件夹，如果文件夹为空，使用`rmdir dir`即可，如果文件夹不为空，使用`rm -rf dir`删除该目录以及该目录下的所有文件 ，`-r`--是删除目录，`-f`意思是--强制删除，不提示。(-f:force -r:recursive)
> `man -k rmdir`查看rmdir命令作用，`info rmdir`查看rmdir命令详细信息

### 显示隐藏文件
<!--more-->
```
defaults write com.apple.finder AppleShowAllFiles Yes && killall Finder 
```

### 不显示隐藏文件
```
defaults write com.apple.finder AppleShowAllFiles No && killall Finder 
```

### 删除文件夹下的所有 .git 文件
```
find . -name ".git" | xargs rm -Rf　　
```

### brew: command not found
安装brew:
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

