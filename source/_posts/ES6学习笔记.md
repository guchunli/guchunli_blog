---
title: ES6学习笔记
date: 2017-07-11 09:13:09
categories: 笔记
tags: [ReactNative]
---

## let 和 const 命令
### let
* 基本用法
用来声明变量。用法类似于var，但是所声明的变量，只在let命令所在的代码块内有效。
`for语句设置循环变量部分与循环体是两个作用域。for循环设置循环变量的那部分是一个父作用域，而循环体内部是一个单独的子作用域。`
<!--more-->```

* 不存在变量提升
`var声明的变量存在的问题：1.变量提升 2.for语句中的循环变量为全局变量`
var声明的变量在声明前使用已经存在，但没有值；let声明的变量在声明前是不存在的。
var命令会发生”变量提升“现象，即变量可以在声明之前使用，值为undefined。
let命令声明的变量一定要在声明后使用，否则报错。


* 暂时性死区
暂时性死区：在代码块内，使用let命令声明变量之前，该变量都是不可用的。let声明的变量在块级作用域内被绑定，不受外部影响。
ES6 明确规定，如果区块中存在let和const命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就使用这些变量，就会报错。

* 不允许重复声明
let不允许在相同作用域内，重复声明同一个变量。
```
function func(arg) {
let arg; // 报错
}

function func(arg) {
{
let arg; // 不报错
}
}
```

### 块级作用域
* 外层代码块不受内层代码块的影响。外层作用域无法读取内层作用域的变量。
* 内层作用域可以定义外层作用域的同名变量。
* 允许在块级作用域之中声明函数，块级作用域之中，函数声明语句的行为类似于let，在块级作用域之外不可引用。

浏览器的 ES6 环境中：
允许在块级作用域内声明函数。（只在使用大括号的情况下成立，如果没有使用大括号，就会报错。）
函数声明类似于var，即会提升到全局作用域或函数作用域的头部。
同时，函数声明还会提升到所在的块级作用域的头部。

应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。
// 函数声明语句
{
let a = 'secret';
function f() {
return a;
}
}

// 函数表达式
{
let a = 'secret';
let f = function () {
return a;
};
}

### const
`引用第三方库时声明的变量用const修饰可以避免重命名导致错误。`
* const声明一个只读的常量。一旦声明变量，就必须立即初始化。
* const的作用域与let命令相同：只在声明所在的块级作用域内有效。不提升，暂时死区。不可重复声明。

本质：不是变量的值不得改动，而是变量指向的那个内存地址所保存的数据不得改动。
对于基本数据类型等同于常量。
对于复合类型，变量指向的内存地址，保存的只是一个指向实际数据的指针，const只能保证这个指针是固定的（即总是指向另一个固定的地址），至于它指向的数据结构是不是可变的，就完全不能控制了。
用const声明对象，这个地址指向一个对象，不可变的只是这个地址，即不能把对象指向另一个地址，但对象本身是可变的，所以依然可以为其添加新属性。如果真的想将对象冻结，应该使用Object.freeze方法。

### ES6 声明变量的六种方法
ES5 只有两种声明变量的方法：var命令和function命令。
ES6 let和const命令，import和class命令。

### 顶层对象的属性
顶层对象：在浏览器环境指的是window对象，在 Node 指的是global对象。ES5 之中，顶层对象的属性与全局变量是等价的。
ES6 规定：
* var命令和function命令声明的全局变量，依旧是顶层对象的属性；
* let命令、const命令、class命令声明的全局变量，不属于顶层对象的属性。

### global 对象
* this
全局环境中，this会返回顶层对象。但是，Node 模块和 ES6 模块中，this返回的是当前模块。
函数里面的this，如果函数不是作为对象的方法运行，而是单纯作为函数运行，this会指向顶层对象。但是，严格模式下，这时this会返回undefined。

保证各种环境里面，global对象都是存在的：
```
// ES6 模块的写法
import shim from 'system.global/shim'; shim();
```
将顶层对象放入变量global：
```
// ES6 模块的写法
import getGlobal from 'system.global';
const global = getGlobal();
```

## 变量的解构赋值
### 数组的解构赋值
解构：ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值。
本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。如果解构不成功，变量的值就等于undefined。
```
let [x, y, ...z] = ['a'];
x // "a"
y // undefined
z // []
```

不完全解构：等号左边的模式，只匹配一部分的等号右边的数组。这种情况下，解构依然可以成功。
```
let [x, y] = [1, 2, 3];
```

如果等号的右边不是数组，不是可遍历的结构，，那么将会报错。因为等号右边的值，要么转为对象以后不具备 Iterator 接口（前五个表达式），要么本身就不具备 Iterator 接口（最后一个表达式）。
```
// 报错
let [foo] = 1;
let [foo] = false;
let [foo] = NaN;
let [foo] = undefined;
let [foo] = null;
let [foo] = {};
```

Set 结构，也可以使用数组的解构赋值。只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。Generator 函数，原生具有 Iterator 接口，解构赋值会依次从这个接口获取值

#### default默认值
```
let [x = 1] = [undefined];
```
注意，ES6 内部使用严格相等运算符（===），判断一个位置是否有值。所以，只有当一个数组成员严格等于undefined，默认值才会生效。
* 如果默认值是一个表达式，那么这个表达式是惰性求值的，即只有在用到的时候，才会求值。
```
function f() {
console.log('aaa');
}

let [x = f()] = [1]; //x能取到值，所以函数f不会执行
```
* 默认值可以引用解构赋值的其他变量，但该变量必须已经声明。
```
let [x = y, y = 1] = [];     // ReferenceError: y is not defined
```

### 对象的解构赋值
对象的解构与数组有一个重要的不同：数组的元素是按次序排列的，变量的取值由它的位置决定；而对象的属性没有次序，变量必须与属性同名，才能取到正确的值。
* 对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。对象的解构赋值是下面形式的简写：
```
let { a: c, b: d } = { a: "aaa", b: "bbb" };
```
* 如果变量名与属性名不一致，可以写成下面这样：
```
let { a: c } = { a: 'aaa', b: 'bbb' };
c // "aaa"
```

* 与数组一样，解构也可以用于嵌套结构的对象。
let obj = {
p: [
'Hello',
{ y: 'World' }
]
};
// let { p: [x, { y }] } = obj; //这时p是模式，不是变量，因此不会被赋值
let { p, p: [x, { y }] } = obj; //如果p也要作为变量赋值，可以写成这样
x // "Hello"
y // "World"
p // ["Hello", {y: "World"}]

* 如果解构模式是嵌套的对象，而且子对象所在的父属性不存在，那么将会报错。
```
// 报错
let {foo: {bar}} = {baz: 'baz'};
```

* 因为 JavaScript 引擎会将{x}理解成一个代码块，从而发生语法错误。只有不将大括号写在行首，避免 JavaScript 将其解释为代码块，才能解决这个问题。
```
let x;
{x} = {x: 1};// SyntaxError: syntax error
({x} = {x: 1});// 正确的写法
```
* 由于数组本质是特殊的对象，因此可以对数组进行对象属性的解构。类似于index取值

### 字符串的解构赋值
字符串被转换成了一个类似数组的对象。
* 可对length进行解构赋值
```
let {length : len} = 'hello';
len // 5
```

### 数值和布尔值的解构赋值

### 函数参数的解构赋值
解构赋值时，如果等号右边是数值和布尔值，则会先转为对象。
```
let {toString: s} = 123;
s === Number.prototype.toString // true

let {toString: s} = true;
s === Boolean.prototype.toString // true
```
解构赋值的规则是，只要等号右边的值不是对象或数组，就先将其转为对象。由于undefined和null无法转为对象，所以对它们进行解构赋值，都会报错。
```
let { prop: x } = undefined; // TypeError
let { prop: y } = null; // TypeError
```

* 为函数move的参数指定默认值，而不是为变量x和y指定默认值
function move({x, y} = { x: 0, y: 0 }) {
return [x, y];
}

move({x: 3}); // [3, undefined]
move({}); // [undefined, undefined]
move(); // [0, 0]


### 圆括号问题
ES6 的规则是，只要有可能导致解构的歧义，就不得使用圆括号。建议只要有可能，就不要在模式中放置圆括号。
不得使用圆括号的情况：
1.变量声明语句
```
// 全部报错
let [(a)] = [1];

let {x: (c)} = {};
let ({x: c}) = {};
let {(x: c)} = {};
let {(x): c} = {};

let { o: ({ p: p }) } = { o: { p: 2 } };
```
2.函数参数
```
// 报错
function f([(z)]) { return z; }
// 报错
function f([z,(x)]) { return x; }
```
3.赋值语句的模式
```
// 将整个模式放在圆括号之中，导致报错
({ p: a }) = { p: 42 };
([a]) = [5];
// 将一部分模式放在圆括号之中，导致报错
[({ p: a }), { x: c }] = [{}, {}];//
```

可以使用圆括号的情况：赋值语句的非模式部分。
满足：1.是赋值语句，而不是声明语句 2.圆括号都不属于模式的一部分
```
[(b)] = [3]; // 正确
({ p: (d) } = {}); // 正确
[(parseInt.prop)] = [3]; // 正确
```

### 使用解构赋值的情况
* 交换变量值
```
let x = 1;
let y = 2;

[x, y] = [y, x];
```
* 从函数返回多个值
```
function example() {
return [1, 2, 3];
}
let [a, b, c] = example();
```
* 函数参数的定义
```
// 参数是一组有次序的值
function f([x, y, z]) { ... }
f([1, 2, 3]);

// 参数是一组无次序的值
function f({x, y, z}) { ... }
f({z: 3, y: 2, x: 1});
```
* 提取json数据（重要）
```
let jsonData = {
id: 42,
status: "OK",
data: [867, 5309]
};

let { id, status, data: number } = jsonData;
```
* 函数参数的默认值
```
jQuery.ajax = function (url, {
async = true,
beforeSend = function () {},
cache = true,
complete = function () {},
crossDomain = false,
global = true,
// ... more config
} = {}) {
// ... do stuff
};
```
* for...of遍历Map结构
```
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
console.log(key + " is " + value);
}
```
如果只想获取键名，或者只想获取键值，可以写成下面这样。
```
// 获取键名
for (let [key] of map) {
// ...
}

// 获取键值
for (let [,value] of map) {
// ...
}
```
* 加载模块时输入模块的指定模块
```
const { SourceMapConsumer, SourceNode } = require("source-map");
```

## 字符串的扩展
### codePointAt
codePointAt方法是测试一个字符由两个字节还是由四个字节组成的最简单方法。
```
function is32Bit(c) {
return c.codePointAt(0) > 0xFFFF;
}

is32Bit("𠮷") // true
is32Bit("a") // false
```
### fromCodePoint
S6 提供了String.fromCodePoint方法，可以识别大于0xFFFF的字符，弥补了String.fromCharCode方法的不足。
```
String.fromCodePoint(0x20BB7)
// "𠮷"
```
注意：
1·在作用上，fromCodePoint正好与codePointAt方法相反。
2.fromCodePoint方法定义在String对象上，而codePointAt方法定义在字符串的实例对象上。

### 字符串的遍历器接口
```
for (let codePoint of 'foo') {
console.log(codePoint)
}
```

### includes(),startsWith(),endsWith()
### repeat()

### padStart().padEnd()
头部补全，尾部补全。
如果原字符串的长度，等于或大于指定的最小长度，则返回原字符串。
如果省略第二个参数，默认使用空格补全长度。
padStart的常见用途是为数值补全指定位数。另一个用途是提示字符串格式。
```
'09-12'.padStart(10, 'YYYY-MM-DD') // "YYYY-09-12"
```
### matchAll()
### 模板字符串
模板字符串（template string）是增强版的字符串，用反引号（`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。
* 模板字符串中嵌入变量，需要将变量名写在${}之中： `&{变量/表达式/函数}`。如果大括号中是一个对象，将默认调用对象的toString方法。如果模板字符串中的变量没有声明，将报错。
* 模板字符串的空格和换行，都是被保留的，可以使用trim方法消除它。

String.raw()

## 数值的扩展
### Number.isFinite(), Number.isNaN()
Number.isFinite()用来检查一个数值是否为有限的（finite），即不是Infinity。
如果参数类型不是数值，Number.isFinite一律返回false。
Number.isNaN()用来检查一个值是否为NaN。
如果参数类型不是NaN，Number.isNaN一律返回false。

### Number.parseInt(), Number.parseFloat()

### Number.isInteger()

### Number.EPSILON
Number.EPSILON可以用来设置“能够接受的误差范围”。

### Math 对象的扩展
Math.trunc方法用于去除一个数的小数部分，返回整数部分。对于空值和无法截取整数的值，返回NaN。

## 函数的扩展
### 函数参数的默认值
* ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面。
* 只有当函数foo的参数是一个对象时，变量x和y才会通过解构赋值生成。如果函数foo调用时没提供参数，变量x和y就不会生成，从而报错。通过提供函数参数的默认值，就可以避免这种情况。
```
//function foo({x, y = 5}) {
//  console.log(x, y);
//}
//foo() // TypeError: Cannot read property 'x' of undefined

function foo({x, y = 5} = {}) {
console.log(x, y);
}
foo() // undefined 5
```
### rest 参数
### 严格模式
### name 属性
### 箭头函数
### 双冒号运算符
### 尾调用优化
### 函数参数的尾逗号


## 数组的扩展

## 对象的扩展

## Map

## Promise

## Iterator for...of

## aync

## Class

## Module



参考文章：
[ECMAScript 6 入门](http://es6.ruanyifeng.com)
[30分钟掌握ES6/ES2015核心内容](http://www.jianshu.com/p/ebfeb687eb70)


<!--## class-->
<!--用于定义类：class Person{}-->
<!---->
<!--## extends-->
<!--继承类，constructor内定义的方法和属性是实例对象自己的，外的是所有实力对象共享的-->
<!---->
<!--## super-->
<!--必须先调用父类`super()`,才能修改子类的this-->
<!---->
<!--## arrow function-->
<!--```-->
<!--function(i){return i+1;}-->
<!--(i) => i+1 -->
<!--```-->
<!---->
<!--解决函数内this指向全局变量问题：-->
<!--(1)使用self指代this-->
<!--(2)bind(this)-->
<!--(3)=> 使用箭头函数-->
<!---->
<!--## template string-->
<!--\` \` 表示起始，${}引用变量，保留所有空格和缩进-->
<!---->
<!--# rest-->
<!--传递多个参数时：-->
<!--ES5: arguments-->
<!--ES6: 参数列表可写成`...types`-->
<!---->
<!---->
<!--## JS中的作用域-->
<!--### 函数作用域-->
<!--Javascript没有块级作用域，而是函数作用域-->
<!--```-->
<!--var scope="global";  -->
<!--function t(){  -->
<!--console.log(scope);  //undefined-->
<!--var scope="local"  -->
<!--console.log(scope);  //local-->
<!--}  -->
<!--t();   -->
<!--```-->
<!---->
<!--```-->
<!--var name="global";  -->
<!--if(true){  -->
<!--var name="local";  -->
<!--console.log(name)   //local-->
<!--}  -->
<!--console.log(name);  //local-->
<!--```-->
<!---->
<!--```-->
<!--function t(flag){  -->
<!--if(flag){  -->
<!--var s="ifscope";  -->
<!--for(var i=0;i<2;i++)   -->
<!--;  -->
<!--}  -->
<!--console.log(i);  //2-->
<!--console.log(s);  //ifscope-->
<!--}  -->
<!--t(true); -->
<!--```-->
<!---->
<!--### 变量作用域-->
<!--JS中没有用var声明的变量都是全局变量，而且是顶层对象的属性。-->
<!--使用var声明一个变量时，创建的这个属性是不可配置的，也就是说无法通过delete运算符删除。-->
<!---->
<!--### 作用域链-->
<!--```-->
<!--name="lwy";  -->
<!--function t(){  -->
<!--var name="tlwy";  -->
<!--function s(){  -->
<!--var name="slwy";  -->
<!--console.log(name);  -->
<!--}  -->
<!--function ss(){  -->
<!--console.log(name);  -->
<!--}  -->
<!--s();  //slwy-->
<!--ss(); //tlwy-->
<!--}  -->
<!--t();  -->
<!--```-->
<!---->
<!--```-->
<!--var a = [];-->
<!--for (var i = 0; i < 10; i++) {-->
<!--a[i] = function () {-->
<!--console.log(i);-->
<!--};-->
<!--}-->
<!--a[6](); // 10-->
<!--```-->
<!---->
<!--### with语句-->
<!--with语句主要用来临时扩展作用域链，将语句中的对象添加到当前作用域的头部。-->
<!--with语句结束后，作用域链恢复正常。-->
<!--```-->
<!--person={name:"yhb",age:22,height:175,wife:{name:"lwy",age:21}};  -->
<!--with(person.wife){  -->
<!--console.log(name);  //lwy-->
<!--}  -->
<!--```-->
<!---->
<!--## this-->
<!--1.全局代码区域：所有函数之外的区域，指全局对象window（node.js中是global）-->
<!--2.function代码区域：某个函数内的代码，但不包括它所嵌套的函数内的代码-->
<!--函数中的this的含义在函数声明时无法确定，要到运行期才能确定，而且与调用函数的方式有关-->
<!--内部函数可以引用外部函数的局部变量，但是不能直接应用外部函数的this-->
<!--(1)直接调用时，在严格模式下，this会被设为undefined，非严格模式下，this会被设为全局对象window-->
<!--(2)作为方法调用时，this指方法所属的对象-->
<!---->
<!--### do-->
<!--使块级作用域变为表达式，可以用变量接收do{}的返回值-->

