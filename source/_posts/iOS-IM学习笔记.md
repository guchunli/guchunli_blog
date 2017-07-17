---
title: iOS IM学习笔记
date: 2017-04-22 11:19:11
categories: 笔记
tags: [iOS,IM,socket]
---

客户端需要做如下4件事：
* 1.客户端调用 socket(...) 创建socket；
* 2.客户端调用 connect(...) 向服务器发起连接请求以建立连接；
<!--more-->
* 3.客户端与服务器建立连接之后，就可以通过send(...)/receive(...)向客户端发送或从客户端接收数据；
* 4.客户端调用 close 关闭 socket；

服务端需要做如下5件事：
* 1.服务器调用 socket(...) 创建socket；
* 2.服务器调用 listen(...) 设置缓冲区；
* 3.服务器通过 accept(...)接受客户端请求建立连接；
* 4.服务器与客户端建立连接之后，就可以通过 send(...)/receive(...)向客户端发送或从客户端接收数据；
* 5.服务器调用 close 关闭 socket；

可以通过以下几种方式实现：
* 1.系统socket
* 2.基于Scoket原生：代表框架 CocoaAsyncSocket
* 3.基于WebScoket：代表框架 SocketRocket。(npm install ws)
* 4.MQTTKit：MQTTClient(npm install mosca)
* 5.XMPP
* 6.私有协议：基于WebScoket或者Scoket原生进行封装（高效，省流量，安全性高）
其中MQTT和XMPP为聊天协议，它们是最上层的协议，而WebScoket是传输通讯协议。

XMPP常用对象：
XMPPStream：xmpp基础服务类
XMPPRoster：好友列表类
XMPPRosterCoreDataStorage：好友列表（用户账号）在core data中的操作类
XMPPvCardCoreDataStorage：好友名片（昵称，签名，性别，年龄等信息）在core data中的操作类
XMPPvCardTemp：好友名片实体类，从数据库里取出来的都是它
xmppvCardAvatarModule：好友头像
XMPPReconnect：如果失去连接,自动重连
XMPPRoom：提供多用户聊天支持
XMPPPubSub：发布订阅

代码传送门：[IMWithSocket](https://github.com/guchunli/IMWithSocket)

参考原文：[iOS即时通讯，从入门到“放弃”？](http://www.cocoachina.com/ios/20170110/18544.html)
[iOS - XMPP 的使用](http://www.cnblogs.com/QianChia/p/6411914.html#_label3)
