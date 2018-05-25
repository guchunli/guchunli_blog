---
title: git学习笔记
date: 2017-03-03 10:49:42
categories: 学习
tags: [Git]
toc: true
---

## 初始化仓库
> $ git init

<!--more-->
## 提交代码
> $ git add .
> $ git commit -m “”

## 查看状态
> $ git status

## 查看修改
> $ git diff
``` 
-change something
+change
```
> $ git diff > 1.diff
将diff修改写到1.diff文件中

## 查看提交历史
> $ git log

``` 
commit acbcfcfa48c89deb725aae9e03a68a2f5abaa1d4
Author: gcl <155@163.com>
Date:   Tue Feb 7 14:30:11 2017 +0800

change 2

commit 99f733644454644d90b8ee39c6737dc283c78cec
Author: gcl <155@163.com>
Date:   Tue Feb 7 14:27:55 2017 +0800

change readme.txt

commit 58110376e350d36c8df095b17ff2ecb44906eae2
Author: gcl <155@163.com>
Date:   Tue Feb 7 14:25:17 2017 +0800

add readme.txt
```
### 简化版本信息
> $ git log --pretty=oneline 
``` 
acbcfcfa48c89deb725aae9e03a68a2f5abaa1d4 change 2
99f733644454644d90b8ee39c6737dc283c78cec change readme.txt
58110376e350d36c8df095b17ff2ecb44906eae2 add readme.txt
```

### 查看命令历史
> $ git reflog
``` 
acbcfcf HEAD@{0}: reset: moving to acbcfcfa4
99f7336 HEAD@{1}: reset: moving to HEAD^
acbcfcf HEAD@{2}: commit: change 2
99f7336 HEAD@{3}: commit: change readme.txt
5811037 HEAD@{4}: commit (initial): add readme.txt
```

## 回退版本
HEAD指向当前版本，`HEAD^`是上一版本，`HEAD^^`是上上版本，`HEAD~100`是上100个版本
`--hard`是强制抹除修改，未提交的修改会丢失。	
git reset --hard ***慎重使用***
`--mixed`是保存修改，相当于重新提交。
### 回到上一版本
> $ git reset --hard HEAD^  

### 回到某一历史
> $ git reset --hard commitid

## 工作区与暂存区
* `respository文件夹`：工作区
* `.git`：版本库，包含了stage（暂存区），分支master，指针HEAD
* `git add`是把文件修改放到了暂存区，`git commit`把暂存区的内容提交到了分支，没有提交到暂存区的修改是不会被提交到分支的
提交的事件线：`工作区->git add->暂存区->git commit->分支`

## 撤销修改
`git管理的是修改，而不是文件`
* 清空工作区的修改，回到最近一次git commit或git add时的状态（用版本库的版本替换工作区的版本）：git checkout -- readme.txt （注意：git checkout 是切换分支命令）
* 撤销add后，commit前在暂存区的修改，将暂存区的修改放回工作区：git reset HEAD readme.txt（注意：reset可以回退版本，还可以撤销add到暂存区的内容，回退到工作区，如果要撤销在工作区的修改，可以再次checkout -- file）
* 撤销commit之后，push之前：版本回退，git reset
* git push：无法撤销

## 删除文件
### 确定删除
>git rm filename
>git commit

### 撤销删除，找回文件
>git checkout -- filename
注意：`git checkout`是使用版本库中的最新版本替换工作区的版本，即撤销git add/rm之前的修改

## 远程仓库
* 本地库master与远程库origin关联，origin为远程库的默认叫法
> $ git remote add origin git@github.com:XXX/learngit.git（XXX为你的github账户名）
* 将本地库中的所有内容推送到远程库中，加上-u将本地master分支和远程origin分支关联起来，以后推送就可以直接使用git push简化命令
> $ git push -u origin master
* 以后推送使用以下命令
> $ git push origin master
* 把远程库克隆到本地,使用https速度慢，而且每次都要输入口令
> $ git clone ssh或https
* push与pull
    + push：本地 --> 远程，如果本地超前远程，需要push
    + pull：本地 <-- 远程，如果本地落后远程，需要pull 

## 分支管理
`HEAD指向当前分支--master分支，master指向最新提交`
### 创建分支
* 创建分支  
将当前分支中的所有文件拷贝到新分支
> $ git branch second
* 切换到分支  
> $ git checkout second
* 创建+切换到分支  
> $ git checkout -b second
* 查看当前分支  
> $ git branch

### 合并分支
* 合并指定分支到当前分支(主分支)  
> $ git branch master
> $ git merge second
* 撤销合并操作  
> $ git reset
* 合并模式  
    + ”fast forward”模式：合并分支默认使用”fast forward”模式，删除分支会丢掉分支信息，合并后看不出做过合并
    + 普通模式：禁用”fast forward” 使用普通模式，合并后的历史可以看出曾经做过合并，在merge时生成一个新的commit。
    禁用”fast forward”并且加上了commit描述：
    > $ git merge --no-ff -m "merge with no-ff" second
* 如果出现冲突:git status->修改冲突->git add->git commit

* git rebase origin master
如果出现冲突:git status->修改冲突->git add->`git rebase --continue`

```
git merge：用于合并分支
git rebase：用于合并commit
git fetch：用于更新所有分支状态，如远程仓库已添加一个分支，可通过git fetch -p将新分支拉取下来
git pull：用于取回远程主机分支的更新
git pull = git fetch ＋ git merge
```
git rebase：会把本分支的commits顶到最顶端，比如合并feature分支到master上，会把整个 feature 分支移动到 master 分支的后面，有效地把所有 master 分支上新的提交并入过来。但是，rebase 为原分支上每一个提交创建一个新的提交，重写了项目历史，并且不会带来合并提交。
rebase最大的好处是你的项目历史会非常整洁。首先，它不像 git merge 那样引入不必要的合并提交。其次，如上图所示，rebase 导致最后的项目历史呈现出完美的线性——你可以从项目终点到起点浏览而不需要任何的 fork。这让你更容易使用 git log、git bisect 和 gitk 来查看项目历史。

### 查看分支
> $ git log  
* 查看分支合并图
> $ git log --graph
> $ git log --graph --pretty=oneline --abbrev-commit

### 删除分支
> $ git branch -d second
> $ git branch -D second：强行删除一个没有合并过的分支
> $ git push origin --delete second 删除远程分支

### 分支管理策略  
本地分支：master，dev，bug，feature
远程分支：origin，dev
![“插入图片”](/assets/20170303_git/branch.png) 

### 解决冲突
+ 查看冲突
> $ git status
+ 查看分支合并图 git log --graph
> $ git log --graph --pretty=oneline --abbrev-commit
+ 解决冲突
合并分支发生冲突后，解决冲突步骤：查看冲突->修改->提交->合并完成

### bug分支
将当前无法提交到现场先保存起来，使工作区恢复clean状态，修复bug后，再将保存的东西解开
* 将工作现场先保存起来：git stash
* 查看保存现场记录：git stash list
* 恢复工作线程
    + git stash apply ：stash内容不删除，需要使用git stash drop手动删除
    + git stash pop：恢复的同时将stash的内容删除
    + git stash apply stash@{0}：有多个stah时，指定恢复到某个stash

### feature分支
新添加功能时创建feature分支，开发完成合并到主分支，最好在开发新功能时为每一个新功能创建一个分支

### 多人协作
#### 查看远程库
* 查看远程库的信息
> $ git remote
* 查看远程分支详细信息
> $ git remote -v

#### 推送分支
将本地某个分支中的所有提交推送到远程库
> $ git push origin master
一般只推送master和dev分支，其他可以在本地放着

#### 抓取分支
创建本地dev分支：
> $ git checkout -b dev origin/dev
* git pull提示“no tracking information”,设置dev和origin/dev的链接：
> $ git branch --set-upstream dev origin/dev
> $ git pull
> $ git add/commit/push

* 注意：如果git pull时提示“no tracking information”，则需要指定本地master/dev分支与远程origin/dev分支的链接后，才能pull
> $ git branch --set-upstream dev origin/dev  已弃用
> $ git branch --set-upstream-to=origin/dev dev  最新用法

#### 多⼈人协作的⼯工作模式:
（1）git push origin branch-name
（2）如果推送失败,则因为远程分⽀支⽐比你的本地更新,需要先⽤用git pull试图合并; 
（3）如果合并有冲突,则解决冲突,并在本地提交; 
（4）没有冲突或者解决掉冲突后,再⽤用git push origin branch-name推送就能成功! 

## 标签
### 添加标签
* 添加标签
> $ git tag v1.0
* 补加标签
> $ git log --pretty=oneline --abbrev-commit
> $ git tag v0.9 commitid
* 添加说明
用`-a`指定标签名，`-m`指定说明文字：
> $ git tag -a v0.1 -m "version 0.1 released" 3628164
* 私钥签名标签
> $ git tag -s v0.2 -m "signed version 0.2 released" fec145a

### 查看标签
* 查看所有标签
> $ git tag
* 查看标签信息
> $ git show v0.9
`注意，标签不是按时间顺序列出，而是按字母排序的。`

### 推送标签
* 推送某一标签
> $ git push origin v1.0
* 推送本地所有未推送标签
> $ git push origin --tags

### 删除标签
* 删除本地标签
> $ git tag -d v1.0
* 删除远程标签
    + 需要先删除本地标签：
> $ git tag -d v1.0
    + 推送到远程：
> $ git push origin :refs/tags/v1.0

## github使用
### SSH  
``` 
$ ssh-keygen -t rsa -C "xxxxx@xxxxx.com"
# Creates a new ssh key using the provided email
# Generating public/private rsa key pair...
$ cat ~/.ssh/id_rsa.pub
# ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC6eNtGpNGwstc.... 
将public key添加
$ ssh -T git@github.com （github）
ssh -T git@git.oschina.net (oschina)
Welcome to Git@OSC, yourname! 
```
### 为开源仓库贡献代码
#### fork
在GitHub上，可以任意Fork开源仓库
#### 提交修改
自己拥有Fork后的仓库的读写权限，从自己帐号克隆仓库，然后提交到自己的仓库
#### pull request
请求官方接受修改

## 自定义git
### 配置用户名和email  
如果遇到以下问题：
``` 
*** Please tell me who you are.

Run

git config --global user.email "you@example.com"
git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

>>>fatal: unable to auto-detect email address (got 'xxx@xxx.(none)')
```
需要配置`用户名`和`邮箱`：
> $ git config --global user.name "Your Name"
> $ git config --global user.email "email@example.com"

### 给git命令添加颜色
> $ git config --global color.ui true

### 忽略特殊文件  
+ 忽略文件的原则
    * 忽略操作系统自动生成的文件，比如缩略图等；
    * 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的.class文件；
    * 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。
+ 强制添加被忽略的文件
> $ git add -f App.class 

查看想添加的文件被.gitignore中的哪条规则忽略了  

> $ git check-ignore -v App.class 

注意：.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理

### 配置别名
> $ git config --global alias.st status
> $ git config --global alias.co checkout
> $ git config --global alias.ci commit
> $ git config --global alias.br branch
> $ git config --global alias.unstage 'reset HEAD'
> $ git config --global alias.last 'log -1' 
> $ git config --global alias.lg "log --color --graph --

注意：`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用

### 配置文件
每个仓库的Git配置文件都放在.git/config文件中，查看配置文件
> $ cat .git/config
```
[core]
repositoryformatversion = 0
filemode = false
bare = false
logallrefupdates = true
symlinks = false
ignorecase = true
hideDotFiles = dotGitOnly
[remote "origin"]
url = git@xxx
fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
remote = origin
merge = refs/heads/master
```

> $ cat ~/gitconfig
```
[user]
email = xxx
name = xxx
[color]
ui = true
[alias]
st = status
co = checkout
br = branch
[gui]
encoding = gbk

```

要删除别名，直接把对应的行删掉即可
改错了，可以删掉文件重新通过命令配置

## 自己搭建git服务器
（1）安装git
sudo apt-get install git
（2）创建一个git用户，用来运行git服务
sudo adduser git
（3）创建证书登录
收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个
（4）初始化Git仓库
sudo chown -R git:git sample.git
（5）禁用shell登录
出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：
git:x:1001:1001:,,,:/home/git:/bin/bash
改为：
　git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出。
（6）克隆远程仓库
git clone git@server:/srv/sample.git
（7）要方便管理公钥，用Gitosis；
　　要像SVN那样变态地控制权限，用Gitolite。


## 安装可视化工具gitk
gitk是需要tcl/tk支持的，所以安装gitk之前必须安装tcl/tk。
tcl/tk下载：http://www.tcl.tk/software/tcltk/download.html

## git命令汇总
git init 

git add 
git commit 
git rm
git status

git diff 
git log (--pretty=oneling)
git reflog

git checkout -- filename
git reset HEAD filename
git reset --hard HEAD^(commitid)

git add remote origin git/https
git push/pull/clone

git branch second
git checkout second
git checkout -b second
git branch
git branch -d second
git log --graph --pretty=oneline --abbrev-commit

git stash
git stash list
git stash apply
git stash drop
git stash pop
git stash apply stash@{0}

git remote -v
git checkout -b dev origin/dev
git branch --set-upstream-to=origin/dev dev 
git pull

git tag name
git tag tagname commitid
git tag -a tagname -m "description"
git tag 
git show tagname
git push origin tagname
git push origin --tags
git tag -d tagname
git push origin :refs/tags/tagname

ssh-keygen -t rsa -C "xxxxx@xxxxx.com"
cat ~/.ssh/id_rsa.pub
ssh -T git@github.com （github）

git add -f App.class

git config user.name “username”
git config user.email “email”
git config --global alias.st status

[Git的官方网站](http://git-scm.com)

本文为阅读廖雪峰的官方网站中的git教程的学习笔记，仅为自己的一点学习记录，原文写的非常好，作者很厉害，有兴趣的朋友可以阅读原文：[廖雪峰的官方网站的git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000 "廖雪峰的官方网站的git教程")
