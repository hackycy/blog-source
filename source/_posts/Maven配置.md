---
title: Maven配置
date: 2018-09-22 14:36:55
tags:
    - Maven
    - 环境搭建
categories:
    - Maven
---

** Maven基础环境搭建 **

# 环境

- window10
- jdk1.6或以上

<!-- more -->

# 下载Maven

[官网](http://maven.apache.org/download.cgi)

![](downloadmaven.png)

直接选择 apache-maven-3.5.4-bin.zip格式下载解压即可

# 配置环境变量

## MAVEN_HOME

我的解压存放的目录是 ** G:\Maven **，下面目录自行替换

计算机(右键)-属性-高级系统设置-环境变量

新建系统变量 ** MAVEN_HOME ** 
变量值：** G:\Maven **

编辑系统变量 ** Path ** 
添加变量值： ** G:\Maven\bin **

## 检测是否安装成功

git-bash或者命令提示符下，输入
``` bash
$ mvn --version
Apache Maven 3.5.4 (1edded0938998edf8bf061f1ceb3cfdeccf443fe; 2018-06-18T02:33:14+08:00)
Maven home: G:\Maven
Java version: 1.8.0_131, vendor: Oracle Corporation, runtime: G:\Java\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"
```

出现类似即可。

# Maven配置

## 修改本地仓库地址

Maven缺省的本地仓库路径 ` 用户目录/.m2/repository `

打开maven安装目录下的conf目录中的 ` settings.xml ` 文件

搜索 ` localRepository `
![](localrepo.png)
取消注释，修改成想要存放的路径保存即可，如图所示
![](localrepo_later.png)

### IDEA配置

![](idea_localrepo_setting.png)

### Eclipse配置

![](eclipse_localrepo_setting.png)







