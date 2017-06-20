---
title: java开发环境配置
date: 2017-06-06 11:27:51
categories: 笔记
tags: [Java,web]
toc: true
---

Java开发需要：
1.JDK (Java Deverlopment Kit)
Java开发工具包(JDK)是一个用于开发Java应用程序和小程序的软件开发环境。
它包括Java运行时环境(JRE)，编译器/加载程序(JAVA)，编译器(javac)，归档器(jar)，文档生成(Javadoc)以及 Java 开发的其他工具。
2.IDE (Integrated Development Environment)
集成开发环境：MyEclipse,IDEA

# tomcat
## 1.下载：[http://tomcat.apache.org](http://tomcat.apache.org)
## 2.修改权限，否则会提示`permission denny`错误：
```
sudo chmod 755 xxx/bin/*.sh  (xxx表示你tomcat放至的路径) 
```

## 3.将Tomcat写入系统的环境变量PATH中。
<!--more-->
```
$ pico .bash_profile 
```

在打开的页面中，输入以下：
```
export PATH=$PATH:tomcat_dir/bin;  
export PATH=$PATH:tomcat_dir/logs;  
```

其中tomcat_dir为tomcat根目录
接着按下 control+x，再按 y 表示确定修改，最后回车。

验证是否成功
> $ echo $PATH
显示如下:
> $ -bash: /usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Users/fuhua/Documents/Tomcat/apache-tomcat-8.0.30/bin: No such file or directory
需要重新启动计算机，以便完成系统环境变量的配置。

## 4.启动Tomcat.
* 如果你完成将Tomcat写入系统的环境变量PATH中，你可以直接在终端输入 startup.sh ；
* 如果你没有完成，那么就要定位到Tomcat的bin目录中，如在终端输入 
```
$ cd tomcat_dir/bin
$ startup.sh
Tomcat started.
```

* 在浏览器中输入[http://localhost:8080/](http://localhost:8080/)
如果出现Apache Tomcat界面，代表 tomcat 启动成功。


# MySQL
## 安装MySQL
* 下载：[MySQL官网](https://dev.mysql.com/downloads/mysql/)
* 安装完成时记得保存弹出框中的密码，这是mysql root账号和密码
* 系统偏好设置中，点击MySQL开启MySQL Server服务
## 修改root账户密码
* 关闭MySQL Server服务
* 终端输入
```
// 苹果系统下 mysql server 的安装地址
$ cd /usr/local/mysql/bin/
// 登录管理员权限
$ sudo su
// 禁止 mysql 验证功能
sh-3.2# ./mysqld_safe --skip-grant-tables &
//终端输出
[1] 2487
sh-3.2# Logging to '/usr/local/mysql-5.7.18-macos10.12-x86_64/data/192.168.1.143.err'.
2017-06-06T06:35:36.6NZ mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql-5.7.18-macos10.12-x86_64/data
sh-3.2# ./mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3
Server version: 5.7.18 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

mysql> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('123456');
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

## 安装MySQL Workbench
[https://dev.mysql.com/downloads/workbench/](https://dev.mysql.com/downloads/workbench/)
MySQL connections + ：输入connection name创建新链接

## 加入系统环境变量 
```
$ cd /usr/local/mysql/bin
$ ls
//查看目录中是否有mysql
$ vim ~/.bash_profile
//在该文件中添加 PATH=$PATH:/usr/local/mysql/bin
$ source ~/.bash_profile
$ mysql -uroot -p   //登录mysql
//输入密码后，登录成功会显示：
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 22
Server version: 5.7.18 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

可以通过以下名录修改密码：
> $ $ SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpass');

# JDK
## 安装JDK
### 下载：[JDK官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
### 安装
* mac自带jdk，安装目录：/System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
* 自己安装目录：/Library/Java/JavaVirtualMachines/jdk1.7.0_79.jdk/Contents/Home
### 配置jdk环境变量
（1）只针对当前用户
```
$ touch ~/.bash_profile  
$ vim ~/.bash_profile  
```

在编辑器中添加如下内容：
```
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_79.jdk/Contents/Home
CLASSPAHT=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH:
export JAVA_HOME
export CLASSPATH
export PATH
```

使系统变量生效，使用以下命令或重启电脑：
```
$ source .bash_profile
```

查看java版本：
```
$ java -version
```

检查系统变量是否生效：
```
$ echo $JAVA_HOME
```

（2）针对所有用户
```
$ cd /etc
$ sudo vi profile
```

在编辑器最下面添加如下内容：
```
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.7.0_79.jdk/Contents/Home
CLASSPAHT=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_HOME/bin:$PATH:
```

接下来同上：
```
$ source .bash_profile
$ java -version
$ echo $JAVA_HOME
```

