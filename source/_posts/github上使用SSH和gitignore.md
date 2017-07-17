---
title: github上使用SSH和gitignore
date: 2017-03-03 16:52:02
categories: 笔记
tags: [github,SSH,gitignore]
---

### 1.使用SSH
``` 
$ cd ~/.ssh
$ ssh-keygen -t rsa -C "your email address"
$ cat ~/.ssh/id_rsa.pub
$ ssh -T git@github.com  （github） 
$ ssh -T git@git.oschina.net  (oschina)
$ Welcome to Git@OSC, nickname!
```

<!--more-->
### 2.gitignore
``` 
cd /Users/apple/Desktop/code
git clone git地址
cp gitignore路径 .gitignore
git add .
git commit -m "添加 gitignore"
git push
```

注意："cp gitignore路径 .gitignore"一定要加".gitignore"。
