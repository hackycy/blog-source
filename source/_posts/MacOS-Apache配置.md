---
title: MacOS Apache配置
date: 2019-05-04 11:08:05
tags:
    - Apache
    - 环境搭建
categories:
    - Apache
---

# 环境

- macOS Mojave 10.14.4
- Apache/2.4.38

<!-- more -->

# 查看Apache

macOS系统自带Apache软件，我们直接在命令行下查看Apache版本：

``` bash
$ httpd -v
Server version: Apache/2.4.38 (Unix)
Server built:   Feb 10 2019 02:48:38
```

# 启动Apache

命令行直接启动，如果是普通用户下需要`sudo`，普通用户没有权限操作`apachectl`

``` bash
$ sudo apachectl start
$ sudo apachectl stop
$ sudo apachectl restart
```

mac 下 `Apache` 默认的目录在 `/Library/WebServer` 下

# 开启Apache多用户主目录

执行 `sudo vim /etc/apache2/httpd.conf` 打开 `httpd.conf` 文件，然后查找 `userdir`

``` conf
# User home directories
#Include /private/etc/apache2/extra/httpd-userdir.conf
```

``` conf
#LoadModule userdir_module libexec/apache2/mod_userdir.so
```

去掉前面`#`注释即可。

接着再编辑 `/private/etc/apache2/extra/httpd-userdir.conf` 文件，增加内容：

``` conf
UserDir Sites
```

如果有则不用添加了。

# 配置虚拟主机

执行 `sudo vim /etc/apache2/httpd.conf`，查找`vhost`

``` conf
#LoadModule vhost_alias_module libexec/apache2/mod_vhost_alias.so
```

``` conf
# Virtual hosts
#Include /private/etc/apache2/extra/httpd-vhosts.conf
```

去掉前面`#`注释即可。再修改`/private/etc/apache2/extra/httpd-vhosts.conf`配置即可。

# 使用Homebrew代替自带的

由于之前不知道搞了什么，自带的怎么更改都不生效，所以重新安装了。

## 安装apache

``` bash
$ brew tap homebrew/apache
$ brew tap homebrew/php
$ brew install httpd
```

## 配置apache

配置文件路径为`/usr/local/etc/httpd/httpd.conf`，配置的方法和上述自带无差别。只是配置路径发生了变化。

## 配置PHP模块

``` conf
LoadModule php7_module /usr/local/Cellar/php@7.1/7.1.26/lib/httpd/modules/libphp7.so
<IfModule mod_php7.c>

    AddType application/x-httpd-php .php
    AddType application/x-httpd-php-source .phps

    <IfModule mod_dir.c>
        DirectoryIndex index.html index.php
    </IfModule>
</IfModule>
```

找到所在php的so文件添加进配置即可，php版本可根据自己需要更改。


