---
title: Java语言学习笔记
date: 2017-06-09 09:59:09
categories: 笔记
tags: [Java,语法]
toc: true
---

##JAVA基础
### 1.public类
* 一个源文件中只能有一个public类
* 一个源文件可以有多个非public类
<!--more-->
### 2.package/import
编译java包：
> $ javac -d directory javafilename
```
E:\sources> javac -d c:\classes Simple.java
//运行
e:\sources> set classpath=c:\classes;.;
e:\sources> java mypack.Simple
```

OR
```
e:\sources> java -classpath c:\classes mypack.Simple
```

加载类文件或jar文件的方式:
* 加载临时类文件
    + 通过在命令提示符中设置类路径
    + 通过 -classpath 开关选项
* 永久加载类文件
    + 通过在环境变量中设置类路径
    + 通过创建jar文件，其中包含所有类文件，并将jar文件复制到JDK安装目录的jre/lib/ext文件夹中。

### 3.引用类型
对象、数组都是引用数据类型。
所有引用类型的默认值都是null。

### 4.JAVA常量
在 Java 中使用 final 关键字来修饰常量，通常使用大写字母表示常量

### 5. 基本数据类型
* 不能对boolean类型进行类型转换。
* 在把容量大的类型转换为容量小的类型时必须使用强制类型转换 double->float

6.自动类型转换
转换前的数据类型的位数要低于转换后的数据类型。Float->double

7. Java语言支持的变量类型
#### 类变量：独立于方法之外的变量，用 static 修饰。
+ 在类中以static关键字声明，但必须在方法构造方法和语句块之外
+ 静态变量除了被声明为常量外很少使用。常量是指声明为public/private，final和static类型的变量。常量初始化后不可改变。
+ 静态变量储存在静态存储区。经常被声明为常量，很少单独使用static声明变量。
+ 为了对类的使用者可见，大多数静态变量声明为public类型
+ 默认值和实例变量相似。数值型变量默认值是0，布尔型默认值是false，引用类型默认值是null。变量的值可以在声明的时候指定，也可以在构造方法中指定。此外，静态变量还可以在静态语句块中初始化。
+ 类变量被声明为public static final类型时，类变量名称一般建议使用大写字母。如果静态变量不是public和final类型，其命名方式与实例变量以及局部变量的命名方式一致。
+ 静态块在类加载时main之前执行
#### 实例变量：独立于方法之外的变量，不过没有 static 修饰。
+ 当一个对象被实例化之后，每个实例变量的值就跟着确定；
+ 实例变量可以声明在使用前或者使用后；
+ 一般情况下应该把实例变量设为私有。通过使用访问修饰符可以使实例变量对子类可见；
+ 实例变量具有默认值。数值型变量的默认值是0，布尔型变量的默认值是false，引用类型变量的默认值是null。变量的值可以在声明时指定，也可以在构造方法中指定；
+ 实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用完全限定名
#### 局部变量：类的方法中的变量。
+ 访问修饰符不能用于局部变量；
+ 局部变量是在栈上分配的。
+ 局部变量没有默认值，所以局部变量被声明后，必须经过初始化，才可以使用。

#### 实例初始化块
主要有三个规则。 它们如下：
* 在创建类的实例时创建实例初始化程序块。
* 在父类构造函数被调用之后(即在super()构造函数调用之后)调用实例初始化块。
* 实例初始化程序块按它们显示的顺序排列。

### 8．修饰符：访问修饰符、非访问修饰符
+ 修饰符用来定义类、方法或者变量，通常放在语句的最前端，默认default
#### 访问修饰符
（1） public/protected/default/private
（2） private: 只能通过类中公共的 getter 方法被外部类访问
（3） protected: 同一个包中的任何其他类访问，也能够被不同包中的子类访问
+ public: 如果几个相互访问的 public 类分布在不同的包中，则需要导入相应 public 类所在的包
+ 注意：父类中声明为 public 的方法在子类中也必须为 public。
+ 父类中声明为 protected 的方法在子类中要么声明为 protected，要么声明为 public，不能声明为 private。
+ 父类中声明为 private 的方法，不能够被继承。

#### 非访问修饰符
static,abstract,final,synchronized,volatile
（1）final：用来修饰类、方法和变量，final 修饰的类不能够被继承，修饰的方法不能被继承类重新定义，修饰的变量为常量，是不可修改的。
* final 变量：能被显式地初始化并且只能初始化一次。被声明为 final 的对象的引用不能指向不同的对象。但是 final 对象里的数据可以被改变。也就是说 final 对象的引用不能改变，但是里面的值可以改变。final 修饰符通常和 static 修饰符一起使用来创建类常量。
* final 方法：类中的 final 方法可以被子类继承，但是不能被子类修改。
* final 类：final 类不能被继承，没有类能够继承 final 类的任何特性。
* 被声明为 final 类的方法自动地声明为 final，但是实例变量并不是 final
* 在声明时未初始化的静态final变量称为静态空白final变量。 它只能在静态块中初始化。
（2）abstract: 抽象类不能用来实例化对象，声明抽象类的唯一目的是为了将来对该类进行扩充
一个类不能同时被 abstract 和 final 修饰。
抽象方法:是一种没有任何实现的方法，该方法的的具体实现由子类提供。
* 抽象方法不能被声明成 final 和 static。
* 任何继承抽象类的子类必须实现父类的所有抽象方法，除非该子类也是抽象类。
* 如果一个类包含若干个抽象方法，那么该类必须声明为抽象类。抽象类可以不包含抽象方法。抽象类可以包含抽象方法和非抽象方法。
* 抽象方法的声明以分号结尾，例如：public abstract sample()。
在java中有两种实现抽象的方法，它们分别是：
* 抽象类(部分)
* 接口 (完全)
抽象类实现了部分抽象(0到100％)，而接口实现了完全抽象(100％)。
![抽象类与接口](http://o8cfktdb3.bkt.clouddn.com/acAndIfa.png)

### 9.instanceof: name instanceof String
增强for循环：for(声明语句 : 表达式)
switch支持字符串，直到break退出

### 10.装箱/拆箱
装箱：int->number  拆箱：number->int
intValue() valueOf() compareo() equals() parseInt()(string->int) ceil() floor() random()

### 11.Character char的包装类 
isLetter() isDigit() isUpperCase() isWhitespace() toUpperCase() toString()

### 12.String
length() s1.concat(s2)  s = format(”---%f %d %s”,floatVar,.,.) charAt(index) int compareo()(对象，字符串)
boolean contentEquals(StringBuffer)  endsWith() equals()  indexOf(ch) split substring

### 13. StringBuffer/StringBuilder
当对字符串进行修改的时候，需要使用 StringBuffer 和 StringBuilder 类。
StringBuffer(线程安全) StringBuilder(速度优势,不是线程安全的,不能同步访问)  多数情况下建议使用 StringBuilder 类
StringBuffer：append(s) reverse dekete(start,end) replace(start,end,str) capacity() charAt() indexOf() toString substring

### 14.Arrays数组
double[] list = new double[10];  参数：(int[] array)  返回值：int[]  
fill sort equals binarySearch

### 15.Date
date.toString() getime() after before equals compareo   format parse
SimpleDateFormat(E yyyy-MM-dd HH:mm:ss a zzz) PM
Sleep(1000*3)  Calender.getInstance()

### 16.正则表达式
Pattern Matcher
\s+ 多个空格  ^$ 以什么开始  \d+多个数字  ()? 0/1 可选  * 0/n  +1\n   {n} (n,) >=n  . 除\n\r之外的任何单个字符
[xyz] [^xyz] [a-z]  \b字符边界

### 17.function
重载：方法名相同，参数列表不同
构造方法：方法名与类名相同，没有返回值，自定义则默认构造方法失效
可变参数：一个方法中只能指定一个可变参数，它必须是方法的最后一个参数。任何普通的参数必须在它之前声明
（double... numbers）
protected void finalize()
### 18.BufferReader 
read() write() scanner() print() println()

### 19.I/O
FileInputStream FileOutputStream File FileReader FileWrite mkdir mkdirs isDirectory delete()

20.Scanner(System.in)  
next nextLine

21.Exception 
java.lang.Exception Error  Throwable
IOException RuntimeException

### 22.面向对象
#### 继承
extends final修饰的类不能被集成，方法不能被重写  单继承
#### 重写与重载
Override(返回值和形参都不能改变) Overload(方法名字相同，而参数不同。返回类型可以相同也可以不同)   
构造方法不能被重写
最常用的地方就是构造器的重载
#### 多态
父类指向子类对象  Animal a = new Cat()   实现方式：重写，接口，抽象类和抽象方法  变量不能被重写
#### 抽象类
抽象类不能被实例化(初学者很容易犯的错)，如果被实例化，就会报错，编译无法通过。只有抽象类的非抽象子类可以创建对象。
#### 封装
修改属性的可见性来限制对属性的访问（一般限制为private）
对每个值属性提供对外的公共方法访问，也就是创建一对赋取值方法，用于对私有属性的访问
#### 接口
是抽象方法的集合。
有静态、final变量和抽象方法（JAVA8还可以有默认方法，静态方法），接口字段默认是public，static和final，方法默认是public和abstract
类描述对象的属性和方法。接口则包含类要实现的方法。
除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。
一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。
接口中的方法会被隐式的指定为 public abstract，接口中的变量会被隐式的指定为 public static final
类的多重继承是不合法，但接口允许多重继承
#### 包
小写开头
使用类全名描述 或 import(import 声明必须在包声明之后，类声明之前)


## java高级
### 1.一些概念理解
#### JVM(Java虚拟机)
一个抽象机器。它是一个提供可以执行Java字节码的运行时环境的规范。
JVM执行以下主要任务：
* 加载代码
* 验证代码
* 执行代码
* 提供运行时环境
#### JRE（Java Runtime Environment）
用于提供Java运行时环境。它是JVM的实现。它是实际存在的。它包含一组库和JVM在运行时使用的其他文件。
#### JDK（Java Development Kit）
它是实际上存在的。它包含JRE+开发工具。

JDK(JRE(JVM+LIB+OTHER)+IDE)
