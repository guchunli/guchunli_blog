---
title: iOS打包提交APP Store注意事项
date: 2017-04-13 12:20:22
categories: 笔记
tags: [iOS,app store]
---

1.改为正式环境
* 接口改成线上服务器
* 极光推送环境改为生产环境等

2.版本号
target -> general：`Version`和`Build`版本号
<!--more-->
3.签名
target -> general -> signing

4.删除断点，log

5.本地化，语言包

6.Capabilities -> background modes 
如果后台使用了位置，提交信息中应用描述添加免责声明
> 温馨提示：GPS在后台持续运行，会大大降低电池的寿命。

7.edit scheme: run,archive -> `debug` 改为 `release`

8.打包ad-hoc，进行内测：clean -> build -> archive

最后，打正式包，提交APP Store。

