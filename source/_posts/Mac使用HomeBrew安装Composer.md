---
title: Mac使用HomeBrew安装Composer
date: 2019-02-25 10:48:09
tags:
    - Composer
    - Mac
    - Homebrew
    - 环境搭建
categories:
    - PHP
---

# 环境

- MacOS10以上

<!-- more -->

# 安装HomeBrew

homebrew官网 [链接](https://brew.sh/)

``` bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

回车安装即可

# 安装Composer

``` bash
brew install composer
```

安装完毕后composer可能不是最新版本，也可以升级

``` bash
composer self-update
```

# 修改Packagist 镜像

修改 `composer` 的全局配置文件

打开命令行窗口（windows用户）或控制台（Linux、Mac 用户）并执行如下命令：
``` bash
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

# 使用composer

举例我要引用phalcon的开发工具包。在项目文件夹下新建文件composer.json，并添加如下代码

``` composer.json
{
    "require": {
        "phalcon/devtools": "dev-master"
    }
}
```

运行

``` bash
composer update
```

项目文件夹下就会有vendor文件夹，依赖文件都在这下面啦。
使用时直接包含autoload.php就可以了

``` php
require 'vendor/autoload.php';
```

> 更多命令可以直接查看官方文档http://docs.phpcomposer.com/03-cli.html

参考链接：
- https://www.jianshu.com/p/2b96cc9f593e
- https://pkg.phpcomposer.com/
- https://getcomposer.org/

