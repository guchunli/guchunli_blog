---
title: Babel学习笔记
date: 2017-07-11 10:24:51
categories: 笔记
tags: [ReactNative]
---

Babel：ES6转码器。
## 安装
```
npm install babel@5 --save  //安装ES5
npm install babel-core --save  //安装ES6
```
<!--more-->
* 安装插件：
```
npm install babel-preset-es2015
```

* 创建.babelrc文件：
```
{
"presets": ["es2015"]
}
```

* 配置文件.babelrc：用于配置转码规则和插件
```
{
"presets": ["latest","react","stage-2"],
"plugins": []
}
```

```
# 最新转码规则
$ npm install --save-dev babel-preset-latest
# react 转码规则
$ npm install --save-dev babel-preset-react
# 不同阶段语法提案的转码规则（0-3)
$ npm install --save-dev babel-preset-stage-2
```

* 命令行转码babel-cli
```
$ npm install --global babel-cli
```

将babel-cli安装在项目中
```
$ npm install --save-dev babel-cli
```

改写package.json
```
{
// ...
"devDependencies": {
"babel-cli": "^6.0.0"
},
"scripts": {
"build": "babel src -d lib"
},
}
```

执行命令：
```
$ npm run build
```

## 
待补充
