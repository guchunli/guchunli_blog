---
title: cocoapods安装与使用
date: 2017-08-14 15:24:09
categories: 笔记
tags: [cocoapods]
---

## cocoapods的安装
1.安装过程中如果出现以下错误提示：
```
ERROR:  While executing gem ... (OpenSSL::SSL::SSLError)
    hostname "rubygems-china.oss.aliyuncs.com" does not match the server certificate
```
<!--more-->
移除镜像：
```
gem sources --remove https://ruby.taobao.org/
gem sources --remove https://gems.ruby-china.org/
```

使用`https://gems.ruby-china.org/`镜像：
```
gem sources -a https://gems.ruby-china.org/
```

2.查看镜像：
```
gem sources -l
```

3.安装cocoapods：
```
sudo gem install cocoapods
```

### 安装Homebrew
```
//ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ curl -LsSf http://github.com/mxcl/homebrew/tarball/master | sudo tar xvz -C/usr/local --strip 1
Error: /usr/local must be writable!
$ sudo chown -R $(whoami) /usr/local
$ brew update     //更新版本
$ brew doctor     //检查是否安装成功
Your system is ready to brew.
```

### 安装npm和node
```
brew install node
npm -v      //查看npm是否安装成功
node -v     //查看node是否安装成功
```

## cocoapods的使用
```
//拉取最新pod库时间较长
pod setup
vim Podfile
pod install
//配置完成后在 Podfile 所在目录执行更新
pod update
```

Podfile：
```
platform :ios, ’9.0’
target 'xxx' do
use_framework!
pod 'AFNetworking'
pod 'SDWebImage'
pod 'MJExtension'
pod 'MJRefresh'
pod 'MBProgressHUD'
end
```
在swift中配置Podfile文件时要加上 `use_framework!` ，作用是把三方库打包成静态库。

### Project Document-Project Format

