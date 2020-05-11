---
title: MySQL错误集
date: 2018-09-13 12:34:47
tags:
    - Mysql
categories:
    - DBA
keywords:
    - Mysql
    - Error
---

** MYSQL错误集记录 **

<!-- more -->

# ERROR 1215-Cannot add foreign key constraint

先看俩个表结构
![](desc_book.png)
![](desc_reader.png)

对reader表进行添加外键约束book表
``` bash
mysql> ALTER TABLE reader ADD CONSTRAINT fk_book_reader FOREIGN key
    -> reader(book_id) REFERENCES book(book_id);
```
报错：1215-Cannot add foreign key constraint
问题分析：

> 主外键更多的是某表的主键与子表的某个列进行关联，要求是具备相同的数据类型和属性

要求：具备相同的数据类型和约束
发现：unsigned，数字的字符长度不一致。
修改reader表book_id列属性
``` bash
mysql> ALTER TABLE reader MODIFY book_id INT UNSIGNED;
```
再次进行添加外键约束，问题解决。

# ERROR 1075 (42000): Incorrect table definition; there can be only one auto column and it must be defined as a key

引发原因，建表语句：
``` mysql
mysql> create table if not exists user(
    -> id int unsigned auto_increment,
    -> uuid varchar(30) not null,
    -> primary key (uuid,id)
    -> ) engine=InnoDB default charset=utf8;   
```
解决方法：当有一个自增键和另外一个字段需要作为主键时，一定要在定义primary key中先定义自增键，否则报错。
``` mysql
mysql> create table if not exists user(
    -> id int unsigned auto_increment,
    -> uuid varchar(30) not null,
    -> primary key (id,uuid)
    -> ) engine=InnoDB default charset=utf8;
```
