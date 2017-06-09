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

## hexo填坑历程
(1)坑一：hexo命令找不到
```
ERROR Local hexo not found in E:\blog
ERROR Try running: 'npm install hexo --save'
```

解决：按照提示输入`npm install hexo --save`，如果不可以，可能是：
因为安装包中有些内容在墙外，所以可以换淘宝源，或者用
> $ npm install -g hexo-cli --no-optional(**很重要**)
有时候只写`npm install --no-optional`也不行，只能以上命令

如果还是不可以，可能是由于：
.gitignore文件里面忽略了node_modules文件夹，所以这个文件夹没有更新上去。所以用npm重新安装即可，输入以下命令：
```
cd ...\blog
npm install
hexo server
```

(2)坑二：ERROR Deployer not found: git
> $ npm install hexo-deployer-git --save 
然后重新 deploy 即可

(3)坑三：hexo deploy成功，但是页面不显示
解决：
到仓库下的settings下修改GitHub Pages，source选择master分支，显示 `Your site is published at https://XXX.github.io/`即可

(4)坑四：
```
fatal: Not a git repository (or any of the parent directories): .git
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Error: fatal: Not a git repository (or any of the parent directories): .git
```

解决：
检查_config.yml文件deploy的配置如下：
```
type: git
repo: https://github.com/XXX/XXX.github.io.git
```

如果还是不可以，请看下一解决方案。

(5)坑五：Permission to  XXX.github.io.git denied 
解决：
```
$ git config --global user.name "yourname"
$ git config --global user.email "youremail"
```

如果以上做完还是出不来，删掉根目录下的文件夹：.deploy_git，重新deploy就可以了。 
