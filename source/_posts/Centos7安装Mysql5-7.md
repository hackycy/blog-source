---
title: Centos7安装Mysql5.7
date: 2018-09-07 23:53:55
keywords:
    - Mysql
tags:
    - Mysql
    - Centos7
    - 环境搭建
categories:
    - DBA
---

# 环境
- centos7
- virtualbox

<!-- more -->

# 安装

## centos7需要检查是否已经安装了Mariadb
在安装MySQL之前要检查当前环境中是否已经装了Mariadb，如果存在则需要卸载，否则可能导致MySQL安装失败
``` bash
$ rpm -qa | grep mariadb
```
如果输出maraidb等的信息则需要进行卸载，如图
![](rpm_qa_grep_mariadb.png)
需要进行强制卸载
``` bash
$ rpm -e --nodeps mariadb-libs-5.5.56-2.el7.x86_64
```

## Yum Repo

官方[指导](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)
点击[链接](https://dev.mysql.com/downloads/repo/yum/)查看最新的Yum Repository
![](mysql_yum_rpm_repo.png)
``` bash
// 设置rpm
$ rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-1.noarch.rpm
$ yum repolist all | grep mysql
```
如图
![](set_rpm.png)
需要安装5.7版本，则需要选择发布系列,编辑`/etc/yum.repos.d/mysql-community.repo` 文件来选择系列 。这是文件中发布系列的子存储库的典型条目：
``` bash
[mysql80-community]
name=MySQL 8.0 Community Server
baseurl=http://repo.mysql.com/yum/mysql-8.0-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```
找到要配置的子存储库的条目，然后编辑该enabled选项。指定`enabled=0`禁用子存储库，或 `enabled=1`启用子存储库。例如，要安装MySQL 5.7，请确保您拥有`enabled=0`,MySQL 8.0的上述子存储库条目，并且具有 `enabled=1`,5.7系列的条目：
``` bash
# Enable to use MySQL 5.7
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=http://repo.mysql.com/yum/mysql-5.7-community/el/6/$basearch/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
```
您应该只在任何时候为一个发布系列启用子存储库。当启用多个版本系列的子存储库时，Yum将使用最新的系列。

通过运行以下命令并检查其输出来验证是否已启用和禁用了正确的子存储库（对于启用dnf的系统，请使用dnf替换 命令中的 yum）：
``` bash
$ yum repolist enabled | grep mysql
```
![](repolist_enabled_mysql.png)

安装mysql
``` bash
$ yum install -y mysql-community-server
```

# 启动

使用以下命令启动MySQL服务器：
``` bash
$ systemctl start mysqld.service
```
检查MySQL服务器的状态
``` bash
$ systemctl status mysqld.service
```
MySQL服务器初始化（从MySQL5.7开始）：在服务器初始启动时，如果服务器的数据目录为空，则会发生以下情况：
- 服务器已初始化。
- 在数据目录中生成SSL证书和密钥文件。
- 该validate_password插件安装并启用。
- 将'root'@'localhost' 创建一个超级用户帐户。设置超级用户的密码并将其存储在错误日志文件中。要显示它，请使用以下命令：
``` bash
$ grep 'temporary password' /var/log/mysqld.log
```
通过上面命令获取生成的临时密码登录并为超级用户帐户设置自定义密码，尽快更改root密码：
``` bash
$ mysql -uroot -p
```
修改root本地登录密码
``` sql
$ ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```
> 注意
> MySQL的 __ validate_password __ 
> 插件默认安装。这将要求密码包含至少一个大写字母，一个小写字
> 母，一个数字和一个特殊字符，并且密码总长度至少为8个字符。

[实例](mysql_set_root_pwd.png)