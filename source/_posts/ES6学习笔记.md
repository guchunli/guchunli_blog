---
title: ES6学习笔记
date: 2017-07-11 09:13:09
categories: 笔记
tags: [ReactNative,ES6]
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

参考文章：(30分钟掌握ES6/ES2015核心内容)[http://www.jianshu.com/p/ebfeb687eb70]
