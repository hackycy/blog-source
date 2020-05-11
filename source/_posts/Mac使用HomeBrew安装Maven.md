---
title: Mac使用HomeBrew安装Maven
date: 2019-02-20 13:57:24
tags:
    - Maven
    - Mac
    - Homebrew
    - 环境搭建
categories:
    - Maven
---

# 环境

- MacOS10以上
- jdk1.6或以上

<!-- more -->

# 安装HomeBrew

homebrew官网 [链接](https://brew.sh/)

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

回车安装即可

# 安装Maven

``` bash
$ brew search maven
==> Formulae
maven ✔                    maven-shell                maven@3.3
maven-completion           maven@3.2                  maven@3.5

==> Casks
homebrew/cask-fonts/font-maven-pro
$ brew install maven
```

# 验证maven是否安装成功

``` bash 
mvn -v
```

# 配置Maven仓库

setting.xml路径为`/usr/local/Cellar/maven/3.5.0/libexec/conf/settings.xml`
修改即可。

可将`settings.xml`直接拷贝到`.m2`文件夹下，进行配置。

~~如果没有`.m2`文件夹时,运行命令
``` bash
$ mvn help:system
```
然后打开当前用户的目录，可以在其中找到`.m2`文件夹~~
