---
title: hexo创建个人博客
date: 2017-02-18 16:43:01
categories: 笔记
tags: [hexo,个人博客]
keywords: 
---

## 常见命令
###  1.安装hexo
``` 
$ sudo npm install -g hexo
```

###  2.初始化
``` 
$ hexo init
```
<!--more-->
###  3.生成静态页面
``` 
$ hexo generate（hexo g也可以）
```

###  4.启动本地服务，进行文章预览调试,浏览器输入http://localhost:4000即可
``` 
$ hexo server（hexo s也可以）
```

###  5.发布配置
``` 
$ hexo deploy（hexo d也可以）
```

每次部署的步骤，可按以下三步来进行: `hexo clean`，`hexo generate`，`hexo deploy`，或者 `hexo d -g`

###  6.新建文章
``` 
$ hexo new"postName" 
```

###  7.新建页面
``` 
$ hexo new page"pageName" 
```

问题：ERROR Deployer not found: git
解决：npm install hexo-deployer-git --save 
重新 deploy 即可
