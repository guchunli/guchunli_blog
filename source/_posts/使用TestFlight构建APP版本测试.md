---
title: 使用TestFlight构建APP版本测试
date: 2017-12-06 11:48:55
categories: 笔记
tags: [测试]
toc:
---

## 1.两种测试
* 内部测试：测试人员上限25个        无需审核
* 外部测试：测试人员上限2000个    需要审核，一天左右
<!--more-->
* 一个版本有效期是60天，如果要继续使用，在过期前提交新版本。

## 2.构建内部beta测试
iTunes Connect->Users and Roles->add user

* 角色权限

|      | Admin | App Manager  | Developer | Marketer | Sales |
| ------ | :----: | :----: | :----: | :----: | :----: |
| Can be an internal tester | Y |  Y  | Y  | Y | N |
|   can upload a build       | Y |  Y | Y | N | N |
|   can submit a build for external testing  | Y  | Y | N | N | N |

My Apps->Activity->TestFlight->Select Version to Test->Start Testing

## 3.构建外部beta测试
My Apps->Activity->Test Information->填写信息->External Testing->Add New Testers->Add Build To Test->submit
通过审核后，External Testing->build and hit->Start Testing

## 4.开始测试
内部测试： Apple ID 连接到iTunes Connect，新版本上传到iTunes Connect，收到邮件。
外部测试：版本审核通过，Admin或App Manager在iTunes Connect推送给外部测试，收到邮件。

App Store下载TestFlight，打开邮件，点击Start Testing，安装下载。
