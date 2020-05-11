---
title: Tomcat基本使用
date: 2018-11-21 11:02:06
tags:
    - Tomcat
    - 环境搭建
categories:
    - Tomcat
---

# 下载安装

到apache官网。www.apache.org     http://jakarta.apache.org(产品的主页)

安装版：window （exe、msi） linux（rmp）
压缩版：window（rar，zip） linux（tar，tar.gz）** 学习时候使用 **

<!-- more -->

## 运行和关闭tomcat

### 启动软件
a）找到%tomcat%/bin/startup.bat ，双击这个文件
b）弹出窗口，显示信息（不要关闭次窗口）
c）打开浏览器，输出以下地址 http://localhost:8080
d）看到一只猫画面，证明软件启动成功！

### 关闭软件
a）找到%tomcat%/bin/shutdown.bat，双击这个文件即可！
b）打开浏览器，输出以下地址。看到“无法连接”（最好先清空浏览器缓存）

# Tomcat目录结构

## bin

存放常用tomcat命令

## conf

存放server配置文件

## lib

自带jar包

## logs

存放日志

## temp

存放临时文件

## webapps

存放共享资源目录

## work

工作空间

# Tomcat常见问题

## 一闪而过

配置好JDK环境即可，即JAVA_HOME,PATH，CLASSPATH环境变量即可。

## 端口占用的错误

原因： tomcat启动所需的端口被其他软件占用了！
解决办法： 
a）关闭其他软件程序，释放所需端口
b）修改tomcat软件所需端口
找到并修改%tomcat%/conf/server.xml文件

``` xml
 <Connector port="8081" protocol="HTTP/1.1" 
               connectionTimeout="20000" 
               redirectPort="8443" />
```

## CATALINA环境变量问题

原因： tomcat软件启动后，除了查找JAVA_HOME后，还会再查找一个叫CATALINA_HOME变量，这个变量的作用是设置tomcat的根目录。
解决办法：建议不要设置CATALINA_HOME变量。检查如果有的话，清除掉！！！



