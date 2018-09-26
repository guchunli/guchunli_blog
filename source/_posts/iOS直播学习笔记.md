---
title: iOS直播学习笔记
date: 2017-06-29 17:14:34
categories: 笔记
tags: [直播]
---


## 推流
//LFLiveKit,librtmp

<!--more-->
1.安装Homebrew
查看是否已经安装了Homebrew
```
man brew
```

安装Homebrew
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

2.安装nginx
```
//从github下载Nginx到本地,增加home-brew对nginx的扩展
brew tap homebrew/nginx

//安装Nginx服务器和rtmp模块
brew install nginx-full --with-rtmp-module

查看是否安装成功
nginx
```

在浏览器地址栏输入：`http://localhost:8080`
如果出现`Welcome to nginx!`，则表示安装成功

3.配置rtmp
查看nginx配置文件安装在哪
brew info nginx-full

前往`/usr/local/etc/nginx/nginx.conf`，在最后添加以下代码：
```
rtmp {
    server {
        listen 1990;
        application liveApp {
            live on;
            record off;
        }
    }
}
```

重新加载nginx的配置文件
nginx -s reload

4.安装ffmpeg进行推流
brew install ffmpeg

5.使用ffmepg推流测试
```
ffmpeg -re -i (视频全路径) -vcodec copy -f flv (rtmp路径)
ffmpeg -re -i /Users/xxx/Downloads/4.mp4 -vcodec copy -f flv rtmp://localhost:1990/liveApp/room
```

出现错误：
```
[tcp @ 0x7ffd66e08440] Connection to tcp://localhost:1990 failed (Connection refused), trying next address
[flv @ 0x7ffd68005400] FLV does not support sample rate 48000, choose from (44100, 22050, 11025)
[flv @ 0x7ffd68005400] Audio codec mp3 not compatible with flv
Could not write header for output file #0 (incorrect codec parameters ?): Function not implemented
```

原因：flv格式不支持音频码率为48000的，只支持44100，22050，11025的，你得找一个音频码率为这三个中的其中一个的视频或者利用ffmpeg修改音频的码率


6.使用VLC播放rtmp推流
VLC

7.用ffmpeg抓取桌面以及摄像头推流进行直播
```
//查看ffmpeg是否支持对应的设备
ffmpeg -f avfoundation -list_devices true -i ""
//抓取桌面和摄像头进行推流
ffmpeg -f avfoundation -framerate 30 -i "1:0" -f avfoundation -framerate 30 -video_size 640x480 -i "0" -c:v libx264 -preset slow -filter_complex 'overlay=main_w-overlay_w-10:main_h-overlay_h-10' -acodec libmp3lame -ar 44100 -ac 1  -f flv rtmp://localhost:1990/liveApp/room
```

## 搭建WEB服务器
1.安装node.js,NPM
2.利用NPM下载第三方模块（Express和Socket.IO）
创建package.json:
```
"dependencies": {
"express": "^4.14.0",
"socket.io": "^1.4.8"
}
```

执行`npm install`

3.http服务器搭建
创建xxx.js文件，然后执行`node xxx.js`即可
`
// require
// 加载http模块
var http = require('http');

// 创建http服务器
var server = http.createServer(function(request,response){
// response.write('Hello world');
// response.end();
});

// 监听服务器
server.listen(8080,'192.168.0.101');

console.log('监听8080');
`

4.express框架
Express框架建立在node.js内置的http模块上，可以快速地搭建一个Web服务器
```
// 引入express模块
var express = require('express');

// 创建express服务器，创建服务器没有对访问服务器进行处理
var app = express();

// 监听get请求，请求根目录，输出Hello world
app.get('/',function(request,response){
response.send('Hello world');
});

app.post('/',function(request,response){
response.send('Hello world');
});

app.listen(8080,"192.168.0.101");

console.log("监听8080");
```

5.上传下载服务器搭建

## 搭建socket即时通讯服务器
1.WebSocket
WebSocket：服务器和 客户端 都能主动的向对方发送或接收数据
websocket协议头:ws
Socket.IO:是一个完全由JavaScript实现、基于Node.js、支持WebSocket的协议用于实时通信、跨平台的开源框架。
2.导入Socket.IO
3.创建socket
* （1）面向express框架开发，加载express框架，方便处理get,post请求
* （2）因为socket依赖http,创建http服务器，使用http模块.
* （3）可以通过express创建http服务器http.server(express)
* （4）通过http服务器创建socket
* （5）监听http服务器
4.建立socket连接
```
// 监听socket连接
// function参数必填socket
socket.on('connection',function(clientSocket){
console.log('建立连接',clientSocket);
});
```

5.SocketIO发送事件
