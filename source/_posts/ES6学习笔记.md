---
title: ES6学习笔记
date: 2017-07-11 09:13:09
categories: 笔记
tags: [ReactNative]
---

## let
ES5只有全局作用域和函数作用域，没有块级作用域，而let声明的变量，只在let命令所在的代码块内有效
<!--more-->
ES5产生的问题：
```
var a = [];
for (var i = 0; i < 10; i++) {
a[i] = function () {
console.log(i);
};
}
a[6](); // 10
```

(1)let解决：
```
var a = [];
for (let i = 0; i < 10; i++) {
a[i] = function () {
console.log(i);
};
}
a[6](); // 6
```

(2)闭包解决：
```
var a = [];
function iteratorFactory(i){
    var onclick = function(e){
        console.log(i)
    }
    return onclick;
}
for (var i = 0; i < 10; i++){
    a[i] = iteratorFactory(i)
}
a[6](); // 6
```

## const
引用第三方库时声明的变量用const修饰可以避免重命名导致错误

## class
用于定义类：class Person{}

## extends
继承类，constructor内定义的方法和属性是实例对象自己的，外的是所有实力对象共享的

## super
必须先调用父类`super()`,才能修改子类的this

## arrow function
```
function(i){return i+1;}
(i) => i+1 
```

解决函数内this指向全局变量问题：
(1)使用self指代this
(2)bind(this)
(3)=> 使用箭头函数

## template string
\` \` 表示起始，${}引用变量，保留所有空格和缩进

## destructuring解构
按照一定模式从数组和对象中提取值，对变量进行赋值

## default默认值
ES5: type = type || 'value'
ES6: 参数列表中指定`(type = 'value')`

# rest
传递多个参数时：
ES5: arguments
ES6: 参数列表可写成`...types`


## JS中的作用域
### 函数作用域
Javascript没有块级作用域，而是函数作用域
```
var scope="global";  
function t(){  
console.log(scope);  //undefined
var scope="local"  
console.log(scope);  //local
}  
t();   
```

```
var name="global";  
if(true){  
var name="local";  
console.log(name)   //local
}  
console.log(name);  //local
```

```
function t(flag){  
if(flag){  
var s="ifscope";  
for(var i=0;i<2;i++)   
;  
}  
console.log(i);  //2
console.log(s);  //ifscope
}  
t(true); 
```

### 变量作用域
JS中没有用var声明的变量都是全局变量，而且是顶层对象的属性。
使用var声明一个变量时，创建的这个属性是不可配置的，也就是说无法通过delete运算符删除。

### 作用域链
```
name="lwy";  
function t(){  
var name="tlwy";  
function s(){  
var name="slwy";  
console.log(name);  
}  
function ss(){  
console.log(name);  
}  
s();  //slwy
ss(); //tlwy
}  
t();  
```

```
var a = [];
for (var i = 0; i < 10; i++) {
a[i] = function () {
console.log(i);
};
}
a[6](); // 10
```

### with语句
with语句主要用来临时扩展作用域链，将语句中的对象添加到当前作用域的头部。
with语句结束后，作用域链恢复正常。
```
person={name:"yhb",age:22,height:175,wife:{name:"lwy",age:21}};  
with(person.wife){  
console.log(name);  //lwy
}  
```

## this
1.全局代码区域：所有函数之外的区域，指全局对象window（node.js中是global）
2.function代码区域：某个函数内的代码，但不包括它所嵌套的函数内的代码
函数中的this的含义在函数声明时无法确定，要到运行期才能确定，而且与调用函数的方式有关
内部函数可以引用外部函数的局部变量，但是不能直接应用外部函数的this
(1)直接调用时，在严格模式下，this会被设为undefined，非严格模式下，this会被设为全局对象window
(2)作为方法调用时，this指方法所属的对象



# ES6基础知识
## Babel
ES6转码器
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

## 变量
###let
* let：类似于var,声明的变量只在let命令所在的代码块中有效
* for语句设置循环变量部分与循环体是两个作用域
* 不存在变量提升：var声明的变量在声明前使用已经存在，但没有值；let声明的变量在声明前是不存在的
* 暂时性死区：let声明的变量在块级作用域内被绑定，不受外部影响
* let在同一作用域中不可重复声明变量

### 块级作用域{}
> var声明的变量存在的问题：1.变量升级 2.for语句中的循环变量为全局变量
* 块级作用域中可以声明函数，在浏览器中行为类似于var(会提升)，其他环境类似于let

### do
使块级作用域变为表达式，可以用变量接收do{}的返回值

### const
* 声明只读常量，只声明不赋值会报错
* 同let相同的块级作用域内有效，不提升，窜仔暂时性死区，不可重复声明

### ES6声明变量的六中方法
var,function,let,const,import,class

### 顶层对象
浏览器环境，顶层对象指window，Node指glocal。
var,function声明的全局变量是顶层对象属性，let,const,class声明的不属于。

### global
system.global可以在所有环境拿到global。

### 解构赋值
1.数组的解构赋值
* 模式匹配，解构不成功，变量值为undefined；
* 设置默认值，当成员等于undefined时默认值生效

2.对象的解构赋值
* 允许取别名

3.字符串的解构赋值
* 可对length进行解构赋值

4.使用
* 交换变量值
* 函数返回多个值
* 提取json数据
* for...of遍历Map结构
* 加载模块

## 字符串
* includes(),startsWith(),endsWith(),repeat(),padStart().padEnd()
* 模板字符串：\'\'  
* &{变量/表达式、函数}

## 函数

## 数组

## 对象

## Map

## Promise

## Iterator for...of

## aync

## Class

## Module

待补充


参考文章：(30分钟掌握ES6/ES2015核心内容)[http://www.jianshu.com/p/ebfeb687eb70]
[ECMAScript 6 入门](http://es6.ruanyifeng.com/#docs/promise)
