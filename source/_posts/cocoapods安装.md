---
title: cocoapods安装
date: 2017-08-14 15:24:09
categories: 学习
tags: [cocoapods]
---

1.安装过程中如果出现以下错误提示：
```
ERROR:  While executing gem ... (OpenSSL::SSL::SSLError)
    hostname "rubygems-china.oss.aliyuncs.com" does not match the server certificate
```
<!--more-->
移除镜像：
```
sources --remove https://ruby.taobao.org/
sources --remove https://gems.ruby-china.org/
```

使用阿里云镜像：
```
gem sources -a http://rubygems-china.oss.aliyuncs.com
```

2.查看镜像：
```
gem sources -l
```

3.安装cocoapods：
```
sudo gem install cocoapods
```

## 安装Homebrew
```
$ curl -LsSf http://github.com/mxcl/homebrew/tarball/master | sudo tar xvz -C/usr/local --strip 1
Error: /usr/local must be writable!
$ sudo chown -R $(whoami) /usr/local
$ brew update     //更新版本
$ brew doctor     //检查是否安装成功
Your system is ready to brew.
```

## 安装npm和node
```
brew install node
npm -v      //查看npm是否安装成功
node -v     //查看node是否安装成功
```
