---
title: MongoDB安装和使用
date: 2019-05-11 23:38:48
tags:
	- MongoDB
categories:
	- MongoDB
---

# MongoDB简介

MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。

在高负载的情况下，添加更多的节点，可以保证服务器性能。

MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。

MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。

<!-- more -->

# 特点

- MongoDB的提供了一个面向文档存储，操作起来比较简单和容易。
- 你可以在MongoDB记录中设置任何属性的索引 (如：FirstName="Sameer",Address="8 Gandhi Road")来实现更快的排序。
- 你可以通过本地或者网络创建数据镜像，这使得MongoDB有更强的扩展性。
- 如果负载的增加（需要更多的存储空间和更强的处理能力） ，它可以分布在计算机网络中的其他节点上这就是所谓的分片。
- Mongo支持丰富的查询表达式。查询指令使用JSON形式的标记，可轻易查询文档中内嵌的对象及数组。
- MongoDb 使用update()命令可以实现替换完成的文档（数据）或者一些指定的数据字段 。
- Mongodb中的Map/reduce主要是用来对数据进行批量处理和聚合操作。
- Map和Reduce。Map函数调用emit(key,value)遍历集合中所有的记录，将key与value传给Reduce函数进行处理。
- Map函数和Reduce函数是使用Javascript编写的，并可以通过db.runCommand或mapreduce命令来执行MapReduce操作。
- GridFS是MongoDB中的一个内置功能，可以用于存放大量小文件。
- MongoDB允许在服务端执行脚本，可以用Javascript编写某个函数，直接在服务端执行，也可以把函数的定义存储在服务端，下次直接调用即可。
- MongoDB支持各种编程语言:RUBY，PYTHON，JAVA，C++，PHP，C#等多种语言。
- MongoDB安装简单。

# 安装MongoDB Community Edition

本文只介绍Mac安装，其余安装方法请查阅：https://docs.mongodb.com/manual/administration/install-community/

使用Homebrew安装

``` bash
$ brew tap mongodb/brew
$ brew install mongodb-community@4.0
==> Caveats
To have launchd start mongodb/brew/mongodb-community now and restart at login:
  brew services start mongodb/brew/mongodb-community
Or, if you don't want/need a background service you can just run:
  mongod --config /usr/local/etc/mongod.conf
==> Summary
🍺  /usr/local/Cellar/mongodb-community/4.0.9: 20 files, 221.0MB, built in 23 seconds
```

安装后可查看到后面这段文字，为MongoDB的一些配置文件路径

- the [configuration file](https://docs.mongodb.com/manual/reference/configuration-options/) (`/usr/local/etc/mongod.conf`)
- the [`log directory path`](https://docs.mongodb.com/manual/reference/configuration-options/#systemLog.path) (`/usr/local/var/log/mongodb`)
- the [`data directory path`](https://docs.mongodb.com/manual/reference/configuration-options/#storage.dbPath) (`/usr/local/var/mongodb`)

# MongoDB 数据类型

| 数据类型           | 描述                                                         |
| :----------------- | :----------------------------------------------------------- |
| String             | 字符串。存储数据常用的数据类型。在 MongoDB 中，UTF-8 编码的字符串才是合法的。 |
| Integer            | 整型数值。用于存储数值。根据你所采用的服务器，可分为 32 位或 64 位。 |
| Boolean            | 布尔值。用于存储布尔值（真/假）。                            |
| Double             | 双精度浮点值。用于存储浮点值。                               |
| Min/Max keys       | 将一个值与 BSON（二进制的 JSON）元素的最低值和最高值相对比。 |
| Arrays             | 用于将数组或列表或多个值存储为一个键。                       |
| Timestamp          | 时间戳。记录文档修改或添加的具体时间。                       |
| Object             | 用于内嵌文档。                                               |
| Null               | 用于创建空值。                                               |
| Symbol             | 符号。该数据类型基本上等同于字符串类型，但不同的是，它一般用于采用特殊符号类型的语言。 |
| Date               | 日期时间。用 UNIX 时间格式来存储当前日期或时间。你可以指定自己的日期时间：创建 Date 对象，传入年月日信息。 |
| Object ID          | 对象 ID。用于创建文档的 ID。                                 |
| Binary Data        | 二进制数据。用于存储二进制数据。                             |
| Code               | 代码类型。用于在文档中存储 JavaScript 代码。                 |
| Regular expression | 正则表达式类型。用于存储正则表达式。                         |

# MongoDB概念

## 数据库

一个mongodb中可以建立多个数据库。

MongoDB的默认数据库为"db"，该数据库存储在data目录中。

MongoDB的单个实例可以容纳多个独立的数据库，每一个都有自己的集合和权限，不同的数据库也放置在不同的文件中。

**"show dbs"** 命令可以显示所有数据的列表。

数据库也通过名字来标识。数据库名可以是满足以下条件的任意UTF-8字符串。

- 不能是空字符串（"")。
- 不得含有' '（空格)、.、$、/、\和\0 (空宇符)。
- 应全部小写。
- 最多64字节。

有一些数据库名是保留的，可以直接访问这些有特殊作用的数据库。

- **admin**： 从权限的角度来看，这是"root"数据库。要是将一个用户添加到这个数据库，这个用户自动继承所有数据库的权限。一些特定的服务器端命令也只能从这个数据库运行，比如列出所有的数据库或者关闭服务器。
- **local:** 这个数据永远不会被复制，可以用来存储限于本地单台服务器的任意集合
- **config**: 当Mongo用于分片设置时，config数据库在内部使用，用于保存分片的相关信息。

## 文档

文档是一个键值(key-value)对(即BSON)。MongoDB 的文档不需要设置相同的字段，并且相同的字段不需要相同的数据类型，这与关系型数据库有很大的区别，也是 MongoDB 非常突出的特点。

一个简单的文档例子如下：

``` json
{"site":"hackycy.github.io", "name":"博客"}
```

关系型数据库和MongoDB的一些区别

| RDBMS  | MongoDB                           |
| :----- | :-------------------------------- |
| 数据库 | 数据库                            |
| 表格   | 集合                              |
| 行     | 文档                              |
| 列     | 字段                              |
| 表联合 | 嵌入文档                          |
| 主键   | 主键 (MongoDB 提供了 key 为 _id ) |

需要注意的是：

1. 文档中的键/值对是有序的。
2. 文档中的值不仅可以是在双引号里面的字符串，还可以是其他几种数据类型（甚至可以是整个嵌入的文档)。
3. MongoDB区分类型和大小写。
4. MongoDB的文档不能有重复的键。
5. 文档的键是字符串。除了少数例外情况，键可以使用任意UTF-8字符。

文档键命名规范：

- 键不能含有\0 (空字符)。这个字符用来表示键的结尾。
- .和$有特别的意义，只有在特定环境下才能使用。
- 以下划线"_"开头的键是保留的(不是严格要求的)。

## 集合

集合就是 MongoDB 文档组，类似于 RDBMS （关系数据库管理系统：Relational Database Management System)中的表格。

集合存在于数据库中，集合没有固定的结构，这意味着你在对集合可以插入不同格式和类型的数据，但通常情况下我们插入集合的数据都会有一定的关联性。

# 基础命令

https://docs.mongodb.com/manual/crud/

[http://www.runoob.com/mongodb/mongodb-operators.html](http://www.runoob.com/mongodb/mongodb-operators.html)

## 启动服务器

``` bash
$ mongod --dbpath "/usr/local/var/mongodb"
```

mongodb常用启动参数

| 参数                 | 描述                                                         |
| :------------------- | :----------------------------------------------------------- |
| --bind_ip            | 绑定服务IP，若绑定127.0.0.1，则只能本机访问，不指定默认本地所有IP |
| --logpath            | 定义MongoDB日志文件，注意是指定文件不是目录                  |
| --logappend          | 使用追加的方式写日志                                         |
| --dbpath             | 指定数据库路径                                               |
| --port               | 指定服务端口号，默认端口27017                                |
| --serviceName        | 指定服务名称                                                 |
| --serviceDisplayName | 指定服务名称，有多个mongodb服务时执行。                      |
| --install            | 指定作为一个Windows服务安装。                                |

## 命令行连接服务器

``` bash
$ mongo
```

默认连接的是test数据库

## 查询数据库

``` bash
> show dbs;
admin   0.000GB
config  0.000GB
local   0.000GB
```

## 切换/创建数据库

``` bash
> use shop;
switched to db shop
> db
shop
```

## 删除数据库

``` bash
> db.dropDatabase()
```

删除当前数据库，默认为 test，你可以使用 db 命令查看当前数据库名。

## 创建集合

语法：`db.createCollection(name, options)`

``` bash
> db.createCollection("users");
```

## 查询集合

``` bash
> show collections;
```

## 查询数据

语法：`db.集合名.find(条件);`

``` bash
> db.users.find();
{ "_id" : ObjectId("5cd6f9a519ee5a000f483e6f"), "name" : "tom", "age" : 20 }
{ "_id" : ObjectId("5cd6fe7419ee5a000f483e70"), "name" : "jack", "age" : 25 }
> db.users.find({age:{$gt:21}});
{ "_id" : ObjectId("5cd6fe7419ee5a000f483e70"), "name" : "jack", "age" : 25 }
```

- (>) 大于 - $gt
- (<) 小于 - $lt
- (>=) 大于等于 - $gte
- (<= ) 小于等于 - $lte

## 添加数据

语法：`db.集合名.save(对象)`或者`db.集合名.insert(对象)`

``` bash
> db.users.save({name:"jack", age:20})
WriteResult({ "nInserted" : 1 })
> show collections;
users
> db.users.find();
{ "_id" : ObjectId("5cd6f5e919ee5a000f483e6e"), "name" : "jack", "age" : 20 }
```

mongo默认会给我们加入`_id`作为该文档对象的唯一标识

## 删除数据

语法：`db.集合名.remove(条件对象);`

``` bash
> > db.users.remove({name:"jack"});
WriteResult({ "nRemoved" : 1 })
> db.users.find();
```

## 更新数据

语法：`db.集合名.update({匹配条件对象},{$set:{修改后的对象}});`

``` bash
> db.users.save({name:"jack", age:20})
WriteResult({ "nInserted" : 1 })
> db.users.update({name:'jack'},{$set:{name:'tom'}});
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.users.find();
{ "_id" : ObjectId("5cd6f9a519ee5a000f483e6f"), "name" : "tom", "age" : 20 }
```

## 分页

**skip()**

skip()方法来跳过指定数量的数据，skip方法同样接受一个数字参数作为跳过的记录条数。

语法：`db.集合名.find().skip(Number)`

``` bash
> db.users.find();
{ "_id" : ObjectId("5cd6f9a519ee5a000f483e6f"), "name" : "tom", "age" : 20 }
{ "_id" : ObjectId("5cd6fe7419ee5a000f483e70"), "name" : "jack", "age" : 25 }
> db.users.find().skip(1);
{ "_id" : ObjectId("5cd6fe7419ee5a000f483e70"), "name" : "jack", "age" : 25 }
```

**limit()**

该方法可以读取指定数量的数据记录。

语法：`db.集合名.find().limit(Number)`

``` bash
> db.users.find();
{ "_id" : ObjectId("5cd6f9a519ee5a000f483e6f"), "name" : "tom", "age" : 20 }
{ "_id" : ObjectId("5cd6fe7419ee5a000f483e70"), "name" : "jack", "age" : 25 }
> db.users.find().limit(1);
{ "_id" : ObjectId("5cd6f9a519ee5a000f483e6f"), "name" : "tom", "age" : 20 }
```

> 这两个方法可以连用。

## 排序

使用 sort() 方法对数据进行排序

语法：`db.集合名.find().sort({key:排序方式});`

``` bash
> db.users.find()
{ "_id" : ObjectId("5cd6f9a519ee5a000f483e6f"), "name" : "tom", "age" : 20 }
{ "_id" : ObjectId("5cd6fe7419ee5a000f483e70"), "name" : "jack", "age" : 25 }
> db.users.find().sort({age:-1})
{ "_id" : ObjectId("5cd6fe7419ee5a000f483e70"), "name" : "jack", "age" : 25 }
{ "_id" : ObjectId("5cd6f9a519ee5a000f483e6f"), "name" : "tom", "age" : 20 }
```

> 正数代表升序，负数代表降序

## 聚合函数

聚合(aggregate)主要用于处理数据(诸如统计平均值,求和等)，并返回计算后的数据结果。有点类似sql语句中的 count(*)。

| 表达式    | 描述                                           | 实例                                                         |
| :-------- | :--------------------------------------------- | :----------------------------------------------------------- |
| $sum      | 计算总和。                                     | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$sum : "$likes"}}}]) |
| $avg      | 计算平均值                                     | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$avg : "$likes"}}}]) |
| $min      | 获取集合中所有文档对应值得最小值。             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$min : "$likes"}}}]) |
| $max      | 获取集合中所有文档对应值得最大值。             | db.mycol.aggregate([{$group : {_id : "$by_user", num_tutorial : {$max : "$likes"}}}]) |
| $push     | 在结果文档中插入值到一个数组中。               | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$push: "$url"}}}]) |
| $addToSet | 在结果文档中插入值到一个数组中，但不创建副本。 | db.mycol.aggregate([{$group : {_id : "$by_user", url : {$addToSet : "$url"}}}]) |
| $first    | 根据资源文档的排序获取第一个文档数据。         | db.mycol.aggregate([{$group : {_id : "$by_user", first_url : {$first : "$url"}}}]) |
| $last     | 根据资源文档的排序获取最后一个文档数据         | db.mycol.aggregate([{$group : {_id : "$by_user", last_url : {$last : "$url"}}}]) |

## 管道的概念

管道在Unix和Linux中一般用于将当前命令的输出结果作为下一个命令的参数。

MongoDB的聚合管道将MongoDB文档在一个管道处理完毕后将结果传递给下一个管道处理。管道操作是可以重复的。

表达式：处理输入文档并输出。表达式是无状态的，只能用于计算当前聚合管道的文档，不能处理其它的文档。

这里我们介绍一下聚合框架中常用的几个操作：

- `$project`：修改输入文档的结构。可以用来重命名、增加或删除域，也可以用于创建计算结果以及嵌套文档。
- `$match`：用于过滤数据，只输出符合条件的文档。`$match`使用MongoDB的标准查询操作。
- `$limit`：用来限制MongoDB聚合管道返回的文档数。
- `$skip`：在聚合管道中跳过指定数量的文档，并返回余下的文档。
- `$unwind`：将文档中的某一个数组类型字段拆分成多条，每条包含数组中的一个值。
- `$group`：将集合中的文档分组，可用于统计结果。
- `$sort`：将输入文档排序后输出。
- `$geoNear`：输出接近某一地理位置的有序文档。

求当前集合的记录数

``` bash
> db.users.find().count();
2
```

类似于sql语句

``` sql
> select name, count(*) from users group by name;
```

# 方便练习的基础数据

``` mongoldb
db.users.save({contry:'中国',name:'小明',score:77});
db.users.save({contry:'中国',name:'小红',score:88});
db.users.save({contry:'中国',name:'小张',score:99});
db.users.save({contry:'美国',name:'jack',score:45});
db.users.save({contry:'美国',name:'rose',score:67});
db.users.save({contry:'美国',name:'mick',score:89});

db.orders.insert([
   { "_id" : 1, "item" : "almonds", "price" : 12, "quantity" : 2 },
   { "_id" : 2, "item" : "pecans", "price" : 20, "quantity" : 1 },
   { "_id" : 3  }
]);
db.inventory.insert([
   { "_id" : 1, "sku" : "almonds", description: "product 1", "instock" : 120 },
   { "_id" : 2, "sku" : "bread", description: "product 2", "instock" : 80 },
   { "_id" : 3, "sku" : "cashews", description: "product 3", "instock" : 60 },
   { "_id" : 4, "sku" : "pecans", description: "product 4", "instock" : 70 },
   { "_id" : 5, "sku": null, description: "Incomplete" },
   { "_id" : 6 }
]);
```

> 使用命令还是很不方便，开发中尽量使用一些界面式的工具，例如Navicat