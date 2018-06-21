---
title: SVN使用
date: 2017-08-04 15:07:57
categories: 笔记
tags: [SVN]
toc: true
---

## 命令帮助
```
$ svn help  //svn命令帮助
$ svn htlp add  //svn具体功能帮助
```
<!--more-->
## 创建svn版本库
(1)创建仓库路径
```
$ svnadmin create svnrepo
$ cd svnrepo
```

(2)导入项目源码
```
$ svn import [本地路径][目标版本库路径] -m [description]
```

(3)修改配置文件 passwd
* 添加用户名和密码，格式：`用户名 = 密码`
```
$ cd conf/
$ vi passwd
```

* 修改启动项参数 svnserve.conf
```
$ vi svnserve.conf  
```

将下面几行代码打开，将前面的注释`#`删除
```
anon-access = read
auth-access = write
...
password-db = passwd
...
authz-db = authz
```

* 配置用户权限 authz
```
[groups]
# harry_and_sally = harry,sally
# harry_sally_and_joe = harry,sally,&joe
admin = admin   //配置admin
# [/foo/bar]
# harry = rw
# &joe = r
# * =
[/]
@admin=rw       //配置admin读写权限
* = r
# [repository:/baz/fuz]
# @harry_and_sally = rw
# * = r
```

(4)启动svn服务
```
svnserve -d -r svnrepo
```

## 导入项目
* svn import
```
$ cd svndemo
$ svn import svndemo http://... --message "start"
```

## 导出项目
* svn checkout 导出的代码在版本控制下
```
$ svn checkout https://... --username zhangsan
$ svn checkout svn://... --username zhangsan
```

* svn export 导出的代码不在版本控制下
```
$ svn export [-r version] https://... --username zhangsan
```

## 添加新文件
```
& svn add test.txt
```

## 提交修改
```
& svn commit -m "description"
```

## 更新文件
```
$ svn update [-r version] filename
```

## 删除文件
```
$ svn delete svn://... -m "description"
```

## 比较差异
```
$ svn diff [-r version] filename
$ svn diff > file   //输出diff信息到文件
```

## 加锁/解锁
```
$ svn lock -m "description" [--force] filename
$ svn unlock filename
```

## 查看状态
```
$ svn status
```

## 查看日志
* 可以查看文件的所有修改记录和版本号变化
```
$ svn log filename
```

* 查看文件详细信息
```
$ svn info filename
```

## 查看SVN版本库下的文件和目录列表
```
$ svn list svn://...
```

## 创建新目录，加入版本控制
* 可以查看文件的所有修改记录和版本号变化
```
$ svn mkdir [-m "description"] svn://...
```

> 添加完子目录后，一定要回到根目录下更新一下，否则在该目录下提交文件会提示“提交失败”

## 丢弃本地修改
```
$ svn revert [--recursive] filename
$ svn revert --recursive .  //丢弃整个目录的本地修改
```

## 冲突
* 发生冲突时会生成与冲突文件同名的三个文件：.mine, .rOLDREV, .rNEWREV，例如a.txt发生冲突：
```
a.txt
a.txt.mine
a.txt.r1
a.txt.r2
```

* 解决冲突的三种方式
(1)修改冲突，然后执行以下命令：
```
$ svn resolved 
```

(2)使用库里的新版本代码
```
$ cp a.txt.r2 a.txt
$ svn resolved a.txt
```

(3)撤销修改，使用旧版本代码
```
$ svn revert a.txt
```

## 分支
* 一般项目创建三个分支：
(1)trunk是主分支，是日常开发进行的地方,一般包含： 
```
0-Src                       源码
1-DevelopDoc         开发文档
2-ProductDoc           产品文档
3-ThirdPartyTools     第三方工具（插件）
4-Testing                   测试
5-DeployDoc             部署文档
```

(2)branches，开发分支。阶段性的release版本。

(3)tags，标签分支。一般是只读的，存储阶段性的发布版本。

### 创建分支
```
$ svn copy branchA branchB -m "make B banch"
* 使用分支的两种分支
```
(1)先拷贝本地分支然后提交：checkout->copy->commit
```
$ svn checkout serverBranchA
$ svn copy localBranchA localBranchB
$ svn commit -m "make B banch"
```

(2)直接远程拷贝分支：copy->checkout
```
$ svn copy serverBranchA serverBranchB -m "make B banch"
$ svn checkout serverBranchB
```

### 切换分支
```
$ svn info | grep URL   //branchA
$ svn switch serverBranchB
$ svn info | grep URL   //branchB
``

### 合并分支
```
$ svn merge branchA branchB  // 把对branchA的修改合并到分支branchB
```

### 删除分支
```
$ svn delete serverBranchB -m "delete branch B"
```

## svn cleanup

