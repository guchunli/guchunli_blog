---
title: github上使用SSH和gitignore
date: 2017-03-03 16:52:02
categories: 笔记
tags: [github,Git]
toc: true
---

## 使用SSH
### 来到以下路径
``` 
$ cd ~/.ssh
```
<!--more-->
//如果.ssh文件夹不存在，执行`mkdir ~/.ssh` 进行创建

### 生成RSA密钥对
```
$ ssh-keygen -t rsa -C "your email address"
```

### 查看公钥内容
复制粘贴公钥，注意：公钥内容以ssh-rsa开头，你的邮箱结尾：
```
$ cat ~/.ssh/id_rsa.pub
```

### 测试连接
```
$ ssh -T git@github.com  （github） 
$ ssh -T git@git.oschina.net  (oschina)
$ Welcome to Git@OSC, nickname!
```

## gitignore
``` 
cd /Users/apple/Desktop/code
git clone git地址
cp gitignore路径 .gitignore
git add .
git commit -m "添加 gitignore"
git push
```

注意："cp gitignore路径 .gitignore"一定要加".gitignore"。
