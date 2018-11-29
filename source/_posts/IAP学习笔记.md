---
title: IAP学习笔记
copyright: true
date: 2018-11-29 11:18:45
categories: 笔记
tags: [IAP]
toc:
---

## IAPHelper
<!--more-->

## 恢复购买
 在如下两种情况下，你只能使用SKPaymentQueue的restoreCompletedTransactions方法来恢复自动订阅或者非消耗性商品：
 * 1.在消费者拥有的其他设备上安装它们。
 * 2.在相关应用程序被删除的设备上重新安装它们。
 
## 沙箱测试
### 生成共享密钥
进入 Features，点击 In-App Purchases 表格左上角的 View Share Secret。有可能，你还需要点击 Generate Shared Secret 按钮。
  
参考文章：
[IAP开发的那些坑](https://www.jianshu.com/p/ebdeea271352)
[获取共享密钥](https://blog.csdn.net/kmyhy/article/details/75489241)
