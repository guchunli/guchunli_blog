---
title: Java搭建springMVC项目笔记
date: 2017-06-08 09:44:19
categories: 笔记
tags: [Java]
---

# MyEclipse搭建项目
* 创建命名空间namespace
* 新建项目 new -> project
* 添加tomcat,jdk
* 运行
* 访问：http://localhost:8080/project_name/
<!--more-->

```
...
nested exception is org.springframework.core.NestedIOException: ASM ClassReader failed to parse class file - probably due to a new Java class file version that isn't supported yet
...
nested exception is java.lang.IllegalArgumentException
...
```

解决方法：
在项目上右键–properties–java Compile–compiler compliance level

在工程目录下添加config文件夹，下面添加文件：jdbc.properties,spring-mvc.xml,spring-mybatis.xml
![spring-config](/assets/20170609_java/java_config.png)

* jdbc.properties：mysql配置
* spring-mvc.xml
    + context -> base-package: 包的跟路径
    + bean -> prefix: /WEB-INF/page/下放.jsp文件
        mvc -> bean: utl 的根路径
    + spring-mybatis.xml: 
        - context -> base-package: 包的根路径
        - bean -> base-package: dao的根路径
        - aop -> expression: service的根路径
