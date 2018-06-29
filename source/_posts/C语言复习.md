---
title: C语言复习笔记
date: 2018-06-29 15:13:34
categories: 笔记
tags: [C]
toc: true
---

auto const extern goto register volatile

## 数据类型
### 基本类型
#### 常用基本数据类型占用空间（64位机器为例）
char ： 1个字节
int ：4个字节
float：4个字节
double：8个字节
* sizeof(type) 得到对象或类型的存储字节大小，如：sizeof(int)。
<!--more-->
#### 进制
* 默认为10进制 ，10 ，20。
* 以0开头为8进制，045，021。
* 以0b开头为2进制，0b11101101。
* 以0x开头为16进制，0x21458adf。

#### 浮点类型精度
* 单精度常量：2.3f 。
* 双精度常量：2.3，默认为双精度。

#### 字符型常量
用英文单引号括起来，只保存一个字符'a'、'b' 、'*' ，还有转义字符 '\n' 、'\t'。

#### 字符串常量
用英文的双引号引起来 可以保存多个字符："abc"。

#### 类型转换
* 自动转换规则：
a）浮点数赋给整型，该浮点数小数被舍去；
b）整数赋给浮点型，数值不变，但是被存储到相应的浮点型变量中；
* 强制类型转换形式: (类型说明符)(表达式)

### 枚举类型
### void类型
通常用于三种情况：
* 函数返回为空：void exit (int status);
* 函数参数为空：例如 int rand(void);
* 指针指向void：类型为 void * 的指针代表对象的地址，而不是类型。例如，内存分配函数 void *malloc( size_t size ); 返回指向 void 的指针，可以转换为任何数据类型。

### 派生类型

## 变量
带有静态存储持续时间的变量会被隐式初始化为 NULL（所有字节的值都是 0），其他所有变量的初始值是未定义的。

### 变量的声明
变量的声明有两种情况：
1、一种是需要建立存储空间的。例如：int a 在声明的时候就已经建立了存储空间。
2、另一种是不需要建立存储空间的，通过使用extern关键字声明变量名而不定义它。 例如：extern int a 其中变量 a 可以在别的文件中定义的。
除非有extern关键字，否则都是变量的定义。
extern int i; //声明，不是定义
int i; //声明，也是定义

## 常量
整数常量后缀是 U 和 L 的组合，U 表示无符号整数（unsigned），L 表示长整数（long）。后缀可以是大写，也可以是小写，U 和 L 的顺序任意。

### 定义常量
在 C 中，有两种简单的定义常量的方式（一般把常量定义为大写字母形式）：
* 使用 #define 预处理器。
* 使用 const 关键字。

## 存储类
### auto
`auto 存储类是所有局部变量默认的存储类`，auto 只能用在函数内，即 auto 只能修饰局部变量。
### register
register 存储类用于定义存储在寄存器中而不是 RAM 中的局部变量。这意味着变量的最大尺寸等于寄存器的大小（通常是一个词），且不能对它应用一元的 '&' 运算符（因为它没有内存位置）。
* 定义 'register' 并不意味着变量将被存储在寄存器中，它意味着变量可能存储在寄存器中，这取决于硬件和实现的限制。
### static
* 使用 static 修饰局部变量可以在函数调用之间保持局部变量的值。
* static 修饰符也可以应用于全局变量。当 static 修饰全局变量时，会使变量的作用域限制在声明它的文件内。`static 是全局变量的默认存储类`。
### extern
extern 存储类用于提供一个全局变量的引用，全局变量对所有的程序文件都是可见的。当您使用 'extern' 时，对于无法初始化的变量，会把变量名指向一个之前定义过的存储位置。可以这么理解，extern 是用来在另一个文件中声明一个全局变量或函数。

## 运算符
### 位运算符
假设如果 A = 60，且 B = 13，现在以二进制格式表示，它们如下所示：
A = 0011 1100
B = 0000 1101

* &：如果同时存在于两个操作数中，二进制 AND 运算符复制一位到结果中。
* | ：如果存在于任一操作数中，二进制 OR 运算符复制一位到结果中。
* ^：如果存在于其中一个操作数中但不同时存在于两个操作数中，二进制异或运算符复制一位到结果中。
* ~ ：二进制补码运算符是一元运算符，具有"翻转"位效果，即0变成1，1变成0。
* << ：二进制左移运算符。左操作数的值向左移动右操作数指定的位数。    A << 2 将得到 240，即为 1111 0000
* `>>` ：二进制右移运算符。左操作数的值向右移动右操作数指定的位数。    A `>> `2 将得到 15，即为 0000 1111

### 其他运算符
sizeof()  ： 返回变量的大小。返回的结果是size_t类型；    sizeof(a) 将返回 4，其中 a 是整数。
&  ： 返回变量的地址。    &a; 将给出变量的实际地址。
*  ： 指向一个变量。    *a; 将指向一个变量。

### 运算符优先级
| 类别  |  运算符  |   结合性 | 
|:--------:|:------:|:----------:|
|后缀  |   () [] -> . ++ - -   |   从左到右 |
|一元  |   + - ! ~ ++ - - (type)* & sizeof     从右到左 |
|乘除  |   * / %   |  从左到右 |
|加减  |   + -    | 从左到右 |
|移位  |   `<< >>`   |  从左到右 |
|关系   |  `< <= > >=`  |   从左到右 |
|相等   |  == !=  |   从左到右 |
|位与 AND  |   &   |  从左到右 |
|位异或 XOR   |  ^  |   从左到右 |
|位或 OR  |      | 从左到右 |
|逻辑与 AND  |   &&  |   从左到右 |
|逻辑或 OR   |   |  从左到右 |
|条件    | ?:   |  从右到左 |
|赋值  |    |  从右到左 |
|逗号  |   ,   |  从左到右 |


## 函数
```
return_type function_name( parameter list )
{
body of the function
}
```
### 函数参数
当调用函数时，有两种向函数传递参数的方式：
* 传值调用    该方法把参数的实际值复制给函数的形式参数。在这种情况下，修改函数内的形式参数不会影响实际参数。
* 引用调用    通过指针传递方式，形参为指向实参地址的指针，当对形参的指向操作时，就相当于对实参本身进行的操作。

## 作用域
| 变量  |  定义 |   作用域 | 
|:--------:|------|----------|
| 局部变量  |  在某个函数或块的内部声明的变量 |   只能被该函数或该代码块内部的语句使用。局部变量在函数外部是不可知的。 | 
| 全局变量  |  定义在函数外部，通常是在程序的顶部 |   全局变量在整个程序生命周期内都是有效的，可以被任何函数访问。全局变量在声明后整个程序中都是可用的。 | 
| 形式参数  |  函数的参数，被当作该函数内的局部变量，会优先覆盖全局变量 |   作用域 | 

### 初始化局部变量和全局变量
* 当局部变量被定义时，系统不会对其初始化，您必须自行对其初始化。定义全局变量时，系统会自动对其初始化
|数据类型 |   初始化默认值|
|:--------:|:--------:|
| int |   0 |
| char  |  '\0' |
| float  |  0 |
| double  |  0 |
| pointer  |  NULL |

## 数组
### 声明数组
在 C 中要声明一个数组，需要指定元素的类型和元素的数量
```
double balance[10];
```

### 初始化数组
```
double balance[5] = {1000.0, 2.0, 3.4, 7.0, 50.0};
double balance[] = {1000.0, 2.0, 3.4, 7.0, 50.0};
```

### 指向数组的指针
* balance 是一个指向 &balance[0] 的指针，即数组 balance 的第一个元素的地址，*(balance + 4) 是一种访问 balance[4] 数据的合法方式
* 把 balance 的第一个元素的地址 赋值为 p (把第一个元素的地址存储在 p 中)，p 是一个指向 double 型的指针，可以使用 *p、*(p+1)、*(p+2) 等来访问数组元素
```
double balance[5] = {1000.0, 2.0, 3.4, 7.0, 50.0};
double *p;
double balance[10];
//p 是一个指向 double 型的指针
p = balance;

/* 输出数组中每个元素的值 */
printf( "使用指针的数组值\n");
for ( i = 0; i < 5; i++ )
{
printf("*(p + %d) : %f\n",  i, *(p + i) );
}

printf( "使用 balance 作为地址的数组值\n");
for ( i = 0; i < 5; i++ )
{
printf("*(balance + %d) : %f\n",  i, *(balance + i) );
}
```

## 指针
* 内存地址：每一个变量都有一个内存位置，每一个内存位置都定义了可使用连字号（&）运算符访问的地址，它表示了在内存中的一个地址。
### 指针定义
指针是一个变量，其值为另一个变量的地址，即，内存位置的直接地址。就像其他变量或常量一样，您必须在使用指针存储其他变量地址之前，对其进行声明。星号是用来指定一个变量是指针

### 使用指针
使用一元运算符 * 来返回位于操作数所指定地址的变量的值。
```
int  var = 20;

//定义一个指针变量
int  *ip;

//把变量地址赋值给指针(在指针变量中存储 var 的地址)
ip = &var;

//访问指针变量中可用地址的值
printf("Value of *ip variable: %d\n", *ip );
```

### NULL指针
NULL 指针是一个定义在标准库中的值为零的常量。
检查一个空指针：
```
int  *ptr = NULL;
if(ptr)     /* 如果 p 非空，则完成 */
if(!ptr)    /* 如果 p 为空，则完成 */
```

### 指针的算术运算
#### 递增一个指针
在程序中可以使用指针代替数组，递增变量指针，以便顺序访问数组中的每一个元素。
* 变量指针可以递增，而数组不能递增，因为数组是一个常量指针。

#### 递减一个指针
对指针进行递减运算，即把值减去其数据类型的字节数。

### 指针数组
ptr 声明为一个数组，由 MAX 个整数指针组成。因此，ptr 中的每个元素，都是一个指向 int 值的指针。下面的实例用到了三个整数，它们将存储在一个指针数组中。
```
const int MAX = 3;
int  var[] = {10, 100, 200};
int i, *ptr[MAX];

for ( i = 0; i < MAX; i++)
{
    ptr[i] = &var[i]; /* 赋值为整数的地址 */
}
for ( i = 0; i < MAX; i++)
{
    printf("Value of var[%d] = %d\n", i, *ptr[i] );
}
```

用一个指向字符的指针数组来存储一个字符串列表：
```
const int MAX = 4;
const char *names[] = {
    "Zara Ali",
    "Hina Ali",
    "Nuha Ali",
    "Sara Ali",
};
int i = 0;

for ( i = 0; i < MAX; i++)
{
    printf("Value of names[%d] = %s\n", i, names[i] );
}
```

### 指向指针的指针
当一个目标值被一个指针间接指向到另一个指针时，访问这个值需要使用两个星号运算符。
```
int  var;
int  *ptr;
int  **pptr;

var = 3000;

/* 获取 var 的地址 */
ptr = &var;

/* 使用运算符 & 获取 ptr 的地址 */
pptr = &ptr;

/* 使用 pptr 获取值 */
printf("Value of var = %d\n", var );
printf("Value available at *ptr = %d\n", *ptr );
printf("Value available at **pptr = %d\n", **pptr);
```

### 传递指针给函数
通过引用或地址传递参数，使传递的参数在调用函数中被改变。
声明函数参数为指针类型即可
```
#include <stdio.h>
#include <time.h>

void getSeconds(unsigned long *par);

int main ()
{
    unsigned long sec;

    getSeconds( &sec );

    /* 输出实际值 */
    printf("Number of seconds: %ld\n", sec );

    return 0;
    }

void getSeconds(unsigned long *par)
{
    /* 获取当前的秒数 */
    *par = time( NULL );
    return;
}
```

能接受指针作为参数的函数，也能接受数组作为参数：
```
#include <stdio.h>

/* 函数声明 */
double getAverage(int *arr, int size);

int main ()
{
    /* 带有 5 个元素的整型数组  */
    int balance[5] = {1000, 2, 3, 17, 50};
    double avg;

    /* 传递一个指向数组的指针作为参数 */
    avg = getAverage( balance, 5 ) ;

    /* 输出返回值  */
    printf("Average value is: %f\n", avg );

    return 0;
}

double getAverage(int *arr, int size)
{
    int    i, sum = 0;       
    double avg;          

    for (i = 0; i < size; ++i)
    {
        sum += arr[i];
    }

    avg = (double)sum / size;

    return avg;
}
```

### 从函数返回指针
`C 语言不支持在调用函数时返回局部变量的地址，除非定义局部变量为 static 变量`
```
/* 要生成和返回随机数的函数 */
int * getRandom( )
{
    static int  r[10];
    int i;

    /* 设置种子 */
    srand( (unsigned)time( NULL ) );
    for ( i = 0; i < 10; ++i)
    {
        r[i] = rand();
        printf("%d\n", r[i] );
    }

    return r;
}

/* 要调用上面定义函数的主函数 */
int main ()
{
    /* 一个指向整数的指针 */
    int *p;
    int i;

    p = getRandom();
    for ( i = 0; i < 10; i++ )
    {
        printf("*(p + [%d]) : %d\n", i, *(p + i) );
    }

    return 0;
}
```

## 函数指针
函数指针是指向函数的指针变量。通常我们说的指针变量是指向一个整型、字符型或数组等变量，而函数指针是指向函数。
### 函数指针变量的声明
```
typedef int (*fun_ptr)(int,int); // 声明一个指向同样参数、返回值的函数指针类型
```

```
int max(int x, int y)
{
    return x > y ? x : y;
}

int main(void)
{
    /* p 是函数指针 */
    int (* p)(int, int) = & max; // &可以省略
    int a, b, c, d;

    printf("请输入三个数字:");
    scanf("%d %d %d", & a, & b, & c);

    /* 与直接调用函数等价，d = max(max(a, b), c) */
    d = p(p(a, b), c); 

    printf("最大的数字是: %d\n", d);

    return 0;
}
```

## 回调函数
* 函数指针作为某个函数的参数
* 函数指针变量可以作为某个函数的参数来使用的，回调函数就是一个通过函数指针调用的函数。
* 简单讲：回调函数是由别人的函数执行时调用你实现的函数。
```
// 回调函数
void populate_array(int *array, size_t arraySize, int (*getNextValue)(void))
{
    for (size_t i=0; i<arraySize; i++)
    array[i] = getNextValue();
}

// 获取随机值
int getNextRandomValue(void)
{
    return rand();
}

int main(void)
{
    int myarray[10];
    populate_array(myarray, 10, getNextRandomValue);
    for(int i = 0; i < 10; i++) {
        printf("%d ", myarray[i]);
    }
    printf("\n");
    return 0;
}
```

## 字符串
* 在 C 语言中，字符串实际上是使用 null 字符 '\0' 终止的一维字符数组。因此，一个以 null 结尾的字符串，包含了组成字符串的字符。
* 操作字符串的函数：
strcmp: string compare 
strcat: string catenate 
strcpy: string copy 
strlen: string length 
strlwr: string lowercase 
strupr: string upercase


## 结构体
### 定义
C 数组允许定义可存储相同类型数据项的变量，结构体允许存储不同类型的数据项。
* 在一般情况下，tag、member-list、variable-list 这 3 部分至少要出现 2 个。
```
struct tag { 
member-list
member-list 
member-list  
...
} variable-list ;
```

实例：
```
//1.声明了结构体变量s1，这个结构体并没有标明其标签
struct 
{
int a;
char b;
double c;
} s1;

//2.结构体的标签被命名为SIMPLE,没有声明变量
struct SIMPLE
{
int a;
char b;
double c;
};
//用SIMPLE标签的结构体，另外声明了变量t1、t2、t3
struct SIMPLE t1, t2[20], *t3;

//3.也可以用typedef创建新类型
typedef struct
{
int a;
char b;
double c; 
} Simple2;
//现在可以用Simple2作为类型声明新的结构体变量
Simple2 u1, u2[20], *u3;
```

### 访问结构成员
* 使用成员访问运算符（.）访问结构的成员

## 共用体
### 定义
共用体是一种特殊的数据类型，允许您在相同的内存位置存储不同的数据类型。
```
union [union tag]
{
member definition;
member definition;
...
member definition;
} [one or more union variables];
```

## typedef
### typedef vs #define
#define 是 C 指令，用于为各种数据类型定义别名，与 typedef 类似，但是它们有以下几点不同：
* typedef 仅限于为类型定义符号名称，#define 不仅可以为类型定义别名，也能为数值定义别名，比如您可以定义 1 为 ONE。
* typedef 是由编译器执行解释的，#define 语句是由预编译器进行处理的。

## 预处理器

## 强制类型转换

## 递归

## 可变参数

## 内存管理
