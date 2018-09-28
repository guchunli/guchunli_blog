---
title: python学习笔记
date: 2018-09-27 11:17:38
categories: 笔记
tags: [笔记]
toc: true
---

## 中文编码
 `# -*- coding: UTF-8 -*-` 或者 `#coding=utf-8` 。
## 基础语法
交互式编程
脚本式编程
<!--more-->
### 行和缩进
* python 最具特色的就是使用缩进来表示代码块。不使用大括号 {} 。
* 缩进的空白数量是可变的，但是同一个代码块的语句必须包含相同的缩进空格。
### 多行
可以使用斜杠（ \）将一行的语句分为多行显示，语句中包含 [], {} 或 () 括号就不需要使用多行连接符。
### 注释
单行注释采用 `#` 开头。多行注释使用三个单引号(''')或三个双引号(""")。
### 等待用户输入
`input("按下 enter 键退出，其他任意键显示...\n")`
### 输出
print() 默认输出是换行的，如果要实现不换行需要在变量末尾加上 `end=""`
### 代码组
首行以关键字开始，以冒号( : )结束
### 命令行参数
`$ python -h `

## 变量
* Python 中的变量赋值不需要类型声明。
* 每个变量在使用前都必须赋值，变量赋值以后该变量才会被创建。
* 可以使用del语句删除一些对象的引用。
### 数据类型
#### Numbers（数字）
* int bool float complex
* isinstance 和 type 的区别在于：
    type()不会认为子类是一种父类类型。
    isinstance()会认为子类是一种父类类型。
注意：
* 变量在使用前必须先"定义"（即赋予变量一个值），否则会出现错误
* 可以同时为多个变量赋值，如a, b = 1, 2
* 一个变量可以通过赋值指向不同类型的对象
* 数值的除法包含两个运算符：`/` 返回一个浮点数，`//` 返回一个整数。
* `//` 得到的并不一定是整数类型的数，它与分母分子的数据类型有关系。
#### String（字符串）
* 使用引号('或")来创建字符串
* `\`转义，`r`不转义。
* \：在行尾时作为续行符。也可以使用 三引号"""...""" 或者 '''...''' 跨越多行。
* 三引号：多行，可用在一块HTML或者SQL。
* [头下标:尾下标]：截取，包含头下标的字符，但不包含尾下标的字符。下标是从 0 开始算起，可以是正数或负数，下标可以为空表示取到头或尾。
* []：获取字符串中的字符
* +：连接运算符
* *：重复操作
* in/not in
* %：格式化，`print ("我叫 %s 今年 %d 岁!" % ('小明', 10)) `
注意：
* 不支持单字符类型，单字符在 Python 中也是作为一个字符串使用
* 字符串不能改变。
* python的字串列表有2种取值顺序:
从左到右索引默认0开始的，最大范围是字符串长度少1（0，1，2，3，4，5）
从右到左索引默认-1开始的，最大范围是字符串开头（-6，-5，-4，-3，-2，-1）
#### List（列表）
* `[]` 标识
* 列表中元素的类型可以不相同
* 截取：[头下标:尾下标]，包含头下标的元素，但不包含尾下标的元素。下标是从 0 开始算起，可以是正数或负数，下标可以为空表示取到头或尾。
* 加号（+）是字符串连接运算符，星号（*）是重复操作
* 列表中的元素是可以改变的
* 删除列表元素：`del list1[2]`
* 列表元素个数：`len(list)`
* []：获取列表中的元素
* +：连接运算符
* *：重复操作
* in/not in
* 截取：list[2]  list[-2]  list[1:]
* list(seq)：将元组转换为列表

#### Tuple（元组）
* `()` 标识
* 与列表类似，不同之处在于元组的元素不能修改。元组不能二次赋值，相当于只读列表。
* 元组中的元素类型也可以不相同
* 元组中只包含一个元素时，需要在元素后面添加逗号，`tup1 = (50,)`，否则括号会被当作运算符使用
* 组中的元素值是不允许删除的，但我们可以使用del语句来删除整个元组
* 无关闭分隔符：任意无符号的对象，以逗号隔开，默认为元组
#### Dictionary（字典）
* `{}` 标识
* 键的特性
    * 键(key)必须是唯一的
    * 键必须不可变，所以可以用数字，字符串或元组充当，所以用列表就不行
* str(dict)：输出字典可打印的字符串表示。
* dict.clear() 清空字典，del dict 删除字典
#### Set（集合）
* 是一个无序不重复元素的序列。基本功能是进行成员关系测试和删除重复元素。
* 大括号 { } 或者 set() 函数创建集合，注意：创建一个空集合必须用 set() 而不是 { }，因为 { } 是用来创建一个空字典
* add，update，remove，discard(如果元素不存在，不会发生错误)，clear，
### 类型转换
将数据类型作为函数名

## 运算符
* `//`：取整除 - 返回商的整数部分（向下取整）
* 整数除整数，只能得出整数。如果要得到小数部分，把其中一个数改成浮点数即可。
* ==/!=
* 身份运算符：is/is not
* 成员运算符：in/not in
* 逻辑运算符：and/or/not
* 优先级：赋值>身份>成员>逻辑

## 条件语句
```
if condition_1:
    statement_block_1
elif condition_2:
    statement_block_2
else:
    statement_block_3
```
* 指定任何非0和非空（null）值为true，0 或者 null为false。`
* 执行内容可以多行，以缩进来区分表示同一范围
* 不支持 `switch` 语句，所以多个条件判断，只能用 `elif` 来实现
## 循环语句
* for循环和while循环（在Python中没有do..while循环）
* 循环控制：break continue pass(空语句)
* `while … else` 和 `for … else` 在循环条件为 false 时执行 else 语句块
### for循环
for循环语法：
```
for iterating_var in sequence:
    statements(s)
```
* 通过序列索引迭代：`for index in range(len(fruits)):`
* 遍历数字序列：`for num in range(10,20):`

## 迭代器与生成器
### 迭代器
迭代器是一个可以记住遍历的位置的对象。
迭代器有两个基本的方法：iter() 和 next()。
迭代器对象可以使用常规for语句进行遍历：
```
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
for x in it:
    print (x, end=" ")
```

```
import sys         # 引入 sys 模块

list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象

while True:
    try:
        print (next(it))
    except StopIteration:
        sys.exit()
```

创建一个迭代器：需要在类中实现两个方法 __iter__() 与 __next__() 。


## 函数
```
def functionname( parameters ):
    "函数_文档字符串"
    function_suite
    return [expression]
```

* 在 python 中，类型属于对象，变量是没有类型的
* 在 python 中，strings, tuples, 和 numbers 是不可更改的对象，而 list,dict 等则是可以修改的对象。
* python 中一切都是对象，严格意义我们不能说值传递还是引用传递，我们应该说传不可变对象和传可变对象。
    * 不可变类型：传递的只是参数的值，没有影响参数本身。
    * 可变类型：修改后fun外部的参数也会受影响
### 参数
* 必备参数：须以正确的顺序传入函数。调用时的数量必须和声明时的一样。
* 关键字参数：允许函数调用时参数的顺序与声明时不一致。`printinfo( age=50, name="miki" );`
* 默认参数：`def printinfo( name, age = 35 ):`，调用`printinfo( name="miki" );`
* 不定长参数：加了星号（*）的变量名会存放所有未命名的变量参数。`def printinfo( arg1, *vartuple ):`

### 匿名函数
```
# 可写函数说明
sum = lambda arg1, arg2: arg1 + arg2;
```

## 模块(Module)
一个 Python 文件，以 .py 结尾。
### 模块引入
* import
模块引入：`import module1[, module2[,... moduleN]]`
不管你执行了多少次import，一个模块只会被导入一次。
调用模块中的函数：模块名.函数名

* from…import
从模块中导入一个指定的部分到当前命名空间中
* from…import*
把一个模块的所有内容全都导入到当前的命名空间

### 搜索路径
* 当前目录
* PYTHONPATH
* 默认路径。UNIX下，默认路径一般为/usr/local/lib/python/。

### PYTHONPATH 变量
UNIX 系统，典型的 PYTHONPATH 如下：`set PYTHONPATH=/usr/local/lib/python`

### 命名空间和作用域
* 如果要给函数内的全局变量赋值，必须使用 global 语句

### 特殊函数
* dir() 函数：一个排好序的字符串列表，内容是一个模块里定义过的名字
* reload() 函数：重新导入之前导入过的模块

### 包
* __init__.py 用于标识当前文件夹是一个包。

## I/O
* print
* raw_input：读取键盘输入
* input：可以接收一个Python表达式作为输入
* 文件：open/close/write/read/tell/seek/rename/remove/
* 目录：mkdir/chdir/rmdir/

## 异常处理
如果在try子句执行时没有发生异常，python将执行else语句后的语句（如果有else的话），然后控制流通过整个try语句。
```
try:
<语句>        #运行别的代码
except <名字>：
<语句>        #如果在try部份引发了'name'异常
except <名字>，<数据>:
<语句>        #如果引发了'name'异常，获得附加的数据
else:
<语句>        #如果没有异常发生
```

try-finally 语句无论是否发生异常都将执行最后的代码。
```
try:
<语句>
finally:
<语句>    #退出try时总会执行
raise
```

触发异常：
```
# 定义函数
def mye( level ):
    if level < 1:
        raise Exception,"Invalid level!"
        # 触发异常后，后面的代码就不会再执行
try:
    mye(0)            # 触发异常
except Exception,err:
    print 1,err
else:
    print 2
```

## 面向对象
### 创建类
```
#!/usr/bin/python
# -*- coding: UTF-8 -*-

class Employee:
    '所有员工的基类'
    empCount = 0

    def __init__(self, name, salary):
        self.name = name
        self.salary = salary
        Employee.empCount += 1

    def displayCount(self):
        print "Total Employee %d" % Employee.empCount

    def displayEmployee(self):
        print "Name : ", self.name,  ", Salary: ", self.salary
```

* self代表类的实例，而非类

### 对象销毁（垃圾回收）
* 使用引用计数
* 析构函数 __del__ ：当对象不再被使用时，__del__方法运行

### 继承
* 可以继承多个类
```
class C(A, B):   # 继承类 A 和 B
```

* issubclass()  isinstance()

### 类属性和方法
* __private_attrs：两个下划线开头，声明该属性为私有属性，不能在类的外部被使用或直接访问。在类内部的方法中使用时 self.__private_attrs。
* 类方法必须包含参数 self,且为第一个参数
* __private_method：两个下划线开头，声明该方法为私有方法，不能在类的外部调用。在类的内部调用 self.__private_methods。
* Python不允许实例化的类访问私有数据，但你可以使用 object._className__attrName（ 对象名._类名__私有属性名 ）访问属性

### 单下划线、双下划线、头尾双下划线说明：
* __foo__: 定义的是特殊方法，一般是系统定义名字 ，类似 __init__() 之类的。
* _foo: 以单下划线开头的表示的是 protected 类型的变量，即保护类型只能允许其本身与子类进行访问，不能用于 from module import *。
* __foo: 双下划线的表示的是私有类型(private)的变量, 只能是允许这个类本身进行访问了。

## 正则表达式
`import re`
从字符串的起始位置匹配一个模式：
```
re.match(pattern, string, flags=0)
```

扫描整个字符串并返回第一个成功的匹配：
```
re.search(pattern, string, flags=0)
```

compile 函数：用于编译正则表达式，生成一个正则表达式（ Pattern ）对象，供 match() 和 search() 这两个函数使用。
findall：找到正则表达式所匹配的所有子串

### 正则表达式模式
^                开头
$                结尾
.                匹配除 "\n" 之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，使用 '[.\n]' 的模式。
[...]             一组字符
[^...]           一组字符之外
re*              0/n
re+             1/n
re?             0/1
re{ n}          n 个
re{ n, }        大于等于n个。"o{1,}" 等价于 "o+"。"o{0,}" 则等价于 "o*"。
re{ n, m}     n 到 m 次
|                  或者
\w              匹配字母数字及下划线，等价于[A-Za-z0-9_]。
\W             匹配非字母数字及下划线，等价于 [^A-Za-z0-9_]。
\s              匹配任意空白字符，等价于 [\t\n\r\f]。
\S              匹配任意非空字符，等价于 [^ \f\n\r\t\v]。
\d              匹配任意数字，等价于 [0-9]。
\D              匹配任意非数字，等价于 [^0-9]。


## 网络编程

