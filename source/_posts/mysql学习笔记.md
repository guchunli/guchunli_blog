---
title: mysql学习笔记
date: 2017-06-08 13:41:17
categories: 笔记
tags: [mysql]
toc: true
---

## 数据库
### 创建数据库
```
create database db;
```

<!--more-->
### 删除数据库
```
drop database db;
```

### 查看数据库
```
show databases;
```

### 使用数据库
```
use db;
```

## 表
### 创建表
```
CREATE TABLE db.tb (
`id` INT NOT NULL AUTO_INCREMENT,
`name` VARCHAR(45) NOT NULL,
PRIMARY KEY (`cid`));
```

### 删除表
```
drop table tb
```

### 修改表名
```
RENAME TABLE tb TO new_tbname;
ALTER TABLE tb RENAME TO new_tbname;
```

### 初始化表(清空)
```
TRUNCATE TABLE tb
```

### 复制表：
```
CREATE TABLE tb_name2 SELECT * FROM tb_name;
```
或者部分复制：
```
CREATE TABLE tb_name2 SELECT id,name FROM tb_name;
```

### 查看数据库中可用的表：
```
SHOW TABLES;
```

### 查看表的结构：
```
DESCRIBE tb_name;
SHOW COLUMNS in tb_name; 　　//from也可以
```

## 列/约束
ALTER TABLE tb ADD[CHANGE,RENAME,DROP]
### 增加列/约束
```
alter table tb add column name VARCHAR(45) not null after id,add primary key(id);
```

### 删除列/约束
```
alter table tb drop col2 drop primary key(id);
```

### 修改列/约束
ALTER TABLE tb MODIFY col2 int NOT NULL DEFAULT 100 add col1 int identity(1,1);
ALTER TABLE tb ALTER col2 SET DEFAULT 1000;
ALTER TABLE tb ALTER col2 DROP DEFAULT;

### 约束
primary key,not null,unique,asc/desc/default 0/AUTO_INCREMENT/zerofill

## 单表操作
### 插入
```
insert into tb(col1,col2) values(value1,value2)
```

### 删除
```
delete from tb [WHERE Clause]
```

### 更新
```
update tb set col1=value1 [WHERE Clause]
```

使用MySQL执行update的时候报错：
```
Error Code: 1175. You are using safe update mode and you tried to update a table without a WHERE that uses a KEY column To disable safe mode, toggle the option in Preferences -> SQL Queries and reconnect.
```

解决方法：
执行命令 SET SQL_SAFE_UPDATES = 0;  //修改下数据库模式
如果想要提高数据库安全等级，可以在恢复回原有的设置，执行命令：SET SQL_SAFE_UPDATES = 1;

### 查找
```
select * from tb [WHERE Clause]
```

### where
* =、>、<、<> and/not and  or  is null/is not null
* 分组查询：order by/group by/having
```
select * from tb order by col1,col2 asc/desc
```

* like
```
SELECT field1, field2,...fieldN 
FROM tb
WHERE field1 LIKE condition1 [AND [OR]] filed2 = 'somevalue'
```

* in/not in
```
select * from tb1 where a [not] in (value1,value2,value3)
```

* between/not between

* exists/not exists

* 全选/全不选
where 1=1 表示选择全部    
where 1=2 表示全部不选，

* 分页
```
declare @start int,@end int
@sql  nvarchar(600)
set @sql=’select top’+str(@end-@start+1)+’+from T where rid not in(select top’+str(@str-1)+’Rid from T where Rid>-1)’
exec sp_executesql @sql
```

* 删除重复记录
```
1),delete from tablename where id not in (select max(id) from tablename group by col1,col2,...)
2),select distinct * into temp from tablename
delete from tablename
insert into tablename select * from temp
```

* 列出数据库里所有的表名
```
select name from sysobjects where type='U' // U代表用户
```

* 列出表里的所有的列名
```
select name from syscolumns where id=object_id('TableName')
select column_name,data_type from information_schema.columns
where table_name = '表名'   //查询某一个表的字段和数据类型
```

* 获取当前数据库中的所有用户表
```
select Name from sysobjects where xtype='u' and status>=0
```

## 多表操作
### union
通过组合其他两个结果表（例如 TABLE1 和 TABLE2）并消去表中任何重复行而派生出一个结果表。当 ALL 随 UNION 一起使用时（即 UNION ALL），不消除重复行。两种情况下，派生表的每一行不是来自 TABLE1 就是来自 TABLE2。
```
SELECT expression1, expression2, ... expression_n
FROM tb1
[WHERE conditions]
UNION [ALL | DISTINCT]
SELECT expression1, expression2, ... expression_n
FROM tb2
[WHERE conditions];

DISTINCT: 可选，删除结果集中重复的数据。默认情况下 UNION 操作符已经删除了重复数据，所以 DISTINCT 修饰符对结果没啥影响。
ALL: 可选，返回所有结果集，包含重复数据。
```

### EXCEPT
通过包括所有在 TABLE1 中但不在 TABLE2 中的行并消除所有重复行而派生出一个结果表。当 ALL 随 EXCEPT 一起使用时 (EXCEPT ALL)，不消除重复行。
### INTERSECT
通过只包括 TABLE1 和 TABLE2 中都有的行并消除所有重复行而派生出一个结果表。当 ALL 随 INTERSECT 一起使用时 (INTERSECT ALL)，不消除重复行。

注：使用运算词的几个查询结果行必须是一致的。 

### 分组：group by
count,sum,max,min,avg

### 连接：join
* INNER JOIN（内连接,或等值连接）：获取两个表中字段匹配关系的记录。
```
SELECT a.col1, a.col2, b.col3 FROM tb1 a INNER JOIN tb2 b
ON a.col1 = b.col1;
等价于
SELECT a.col1, a.col2, b.col3 FROM tb1 a, tb2 b 
WHERE a.col1 = b.col1;
```

* LEFT JOIN（左连接）：获取左表所有记录，即使右表没有对应匹配的记录。
```
SELECT a.col1, a.col2, b.col3 FROM tb1 a LEFT JOIN tb2 b 
ON a.col1 = b.col1;
```

* RIGHT JOIN（右连接）： 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。
```
SELECT a.col1, a.col2, b.col3 FROM tb1 a RIGHT JOIN tb2 b 
ON a.col1 = b.col1;
```

## 高级用法
### 索引

### 临时表

### 视图
