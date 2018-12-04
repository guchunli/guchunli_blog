---
title: iOS马甲包混淆
copyright: true
date: 2018-11-30 17:54:54
categories: 笔记
tags: 
toc:
---

## 马甲包混淆
对于OC和C++代码编译出的二进制文件：
删注释是没用的，因为注释是不会被编译进包里
改文件夹或者文件名应该是不太靠谱的，编译的时候会根据路径引用查找
<!--more-->

### 修改
1. 修改类名文件名
在xcode上修改文件名类名，然后在可能引用的地方替换类名和文件名

2. 修改函数名，属性名：代码混淆

3. 修改工程名，target，scheme

4. 修改资源路径和文件名，修改md5值
图片：修改icon、启动页、图标
文本文件：添加随机数量的空格或空行
音视频：待定

5. 修改导出包的名称

6. info.plist会被打包进ipa，最好也多加几个字段上去

### 添加垃圾文件
1. 创建垃圾类
可以写一个单独的头文件include了所有这些生成的垃圾类，然后在外部include了这个单独的头文件

2. 创建垃圾函数
OC头文件的声明必然是在@interface @end之间，实现是在@implementation @end之间

3. 创建垃圾资源文件

## 代码混淆
混淆原理：代码编译阶段将符号（方法名、属性名等）替换成随机生成的字符串。

### 创建shell文件（confuse.sh）并配置相应的运行环境
* 在项目根目录下新建一个文件夹，取文件夹名称为CodeObfuscation。**注意** 这里的文件夹必须在目录中真实存在（Xcode9 New Group会自动创建对应的真实文件夹）
* 在上一步的文件下新建confuse.sh文件：新建文件 -> other -> Shell Script，内容如下：
```
TABLENAME=symbols
SYMBOL_DB_FILE="$PROJECT_DIR/CodeObfuscation/symbols"
STRING_SYMBOL_FILE="$PROJECT_DIR/CodeObfuscation/func.list"
HEAD_FILE="$PROJECT_DIR/CodeObfuscation/codeObfuscation.h"
export LC_CTYPE=C

#维护数据库方便日后作排重
createTable()
{
echo "create table $TABLENAME(src text, des text);" | sqlite3 $SYMBOL_DB_FILE
}

insertValue()
{
echo "insert into $TABLENAME values('$1' ,'$2');" | sqlite3 $SYMBOL_DB_FILE
}

query()
{
echo "select * from $TABLENAME where src='$1';" | sqlite3 $SYMBOL_DB_FILE
}

ramdomString()
{
openssl rand -base64 64 | tr -cd 'a-zA-Z' |head -c 16
}

rm -f $SYMBOL_DB_FILE
rm -f $HEAD_FILE
createTable

touch $HEAD_FILE
echo '#ifndef Demo_codeObfuscation_h
#define Demo_codeObfuscation_h' >> $HEAD_FILE
echo "//confuse string at `date`" >> $HEAD_FILE
cat "$STRING_SYMBOL_FILE" | while read -ra line; do
if [[ ! -z "$line" ]]; then
ramdom=`ramdomString`
echo $line $ramdom
insertValue $line $ramdom
echo "#define $line $ramdom" >> $HEAD_FILE
fi
done
echo "#endif" >> $HEAD_FILE


sqlite3 $SYMBOL_DB_FILE .dump
```
* 给.sh文件添加运行环境：Build Phases -> + -> New Run Script Phase -> $PROJECT_DIR/CodeObfuscation/confuse.sh

### 创建func.list文件
新建文件 -> other -> Empty，文件名：func.list

### 创建codeObfuscation.h文件
新建文件 -> Source -> Header File，文件名：codeObfuscation.h

### 包含codeObfuscation.h到pch文件中，添加要混淆的方法名或属性名到func.list

### 解决报错
command + R运行项目，此时你会发现报错。`Permission denied`错误：.sh文件没有运行权限。
解决：
打开终端，cd到CodeObfuscation文件夹，更改confuse.sh文件运行权限：
```
sudo chmod 777 confuse.sh
```

### 查看结果
codeObfuscation.h 中已经包含了要混淆的方法名和混淆之后的随机字符串

### 确认结果
找到替换的方法名或属性名，点击跳转到定义，会跳转到codeObfuscation.h中，说明替换成功。

参考文章：
[iOS马甲包混淆](https://blog.csdn.net/lyzz0612/article/details/80390362)
[iOS代码混淆教程](https://www.jianshu.com/p/66bb2d45b3c2)
