---
title: Mac缺少freetype解决方案
date: 2019-03-19 11:36:46
tags:
    - Mac
    - Homebrew
    - 环境搭建
    - PHP
categories:
    - PHP
keywords:
    - PHP
    - freetype
---

# 环境

Mac OS X 10.14.3

<!-- more -->

# 问题

最近在使用thinkphp的验证码模块时，出现了一个异常
`Call to undefined function think\captcha\imagettftext()`
没有`imagettftext()`这个函数，原因是因为php缺少gd库中的freetype模块导致的。

知道了原因后，查询了网上大致的方法，大概这么几种
- 重新编译PHP
- 用brew重新安装新的PHP
- https://php-osx.liip.ch 一句话脚本

在尝试了很多遍后，都没有成功。有一篇文章讲到了这些方案比较过时，在Macos10.14版本下更改了安全策略，新增了`Rootless`机制，具体请看我博客中另一篇文章关于`Rootless`的介绍。

# 解决

## 方案一（便携）

使用第三方集成环境例如`MAMP`,`MAMP PRO`,`XAMPP`等

## 方案二（推荐）

### 首先关闭Rootless

关闭步骤
``` text
1、重启 Mac 并按住 Command+R，进入恢复模式
2、打开终端 Terminal
3、输入csrutil disable
```

开启步骤

``` text
1、重启 Mac 并按住 Command+R，进入恢复模式
2、打开终端 Terminal
3、输入csrutil enable
```

### 重新brew安装php

~~这里我试过关闭rootless后使用一句话脚本也没有办法安装成功。也试过一些网上的方案例如~~
``` bash
$ brew install php56 --with-apche --with-freetype
```
~~但是运行出现`Error: invalid option: --with-apche`此错误，寻思没有解决办法。~~

**成功的方案**

``` bash
$ brew install php@7.1 --build-from-source
```

>记住添加`--build-from-source`参数

php 版本可自行通过`brew search php`来查看需要安装所需，运行后出现
``` bash
==> Installing dependencies for php@7.1: pkg-config
==> Installing php@7.1 dependency: pkg-config
==> Downloading https://homebrew.bintray.com/bottles/pkg-config-0.29.2.mojave.bo
######################################################################## 100.0%
==> Pouring pkg-config-0.29.2.mojave.bottle.tar.gz
🍺  /usr/local/Cellar/pkg-config/0.29.2: 11 files, 627.2KB
==> Installing php@7.1
==> Downloading https://php.net/get/php-7.1.26.tar.xz/from/this/mirror
==> Downloading from https://secure.php.net/distributions/php-7.1.26.tar.xz
          #    #    #    #                                         -=O=-      
==> Patching
patching file acinclude.m4
Hunk #1 succeeded at 444 (offset 3 lines).
Hunk #2 succeeded at 459 (offset 3 lines).
Hunk #3 succeeded at 494 (offset 3 lines).
Hunk #4 succeeded at 506 (offset 3 lines).
Hunk #5 succeeded at 2507 (offset 88 lines).
==> ./buildconf --force
==> ./configure --prefix=/usr/local/Cellar/php@7.1/7.1.26 --localstatedir=/usr/l
==> make

==> make install
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set php_ini /usr/local/etc/
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set php_dir /usr/local/shar
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set doc_dir /usr/local/shar
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set ext_dir /usr/local/lib/
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set bin_dir /usr/local/opt/
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set data_dir /usr/local/sha
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set cfg_dir /usr/local/shar
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set www_dir /usr/local/shar
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set man_dir /usr/local/shar
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set test_dir /usr/local/sha
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear config-set php_bin /usr/local/opt/
==> /usr/local/Cellar/php@7.1/7.1.26/bin/pear update-channels
==> Caveats
To enable PHP in Apache add the following to httpd.conf and restart Apache:
    LoadModule php7_module /usr/local/opt/php@7.1/lib/httpd/modules/libphp7.so

    <FilesMatch \.php$>
        SetHandler application/x-httpd-php
    </FilesMatch>

Finally, check DirectoryIndex includes index.php
    DirectoryIndex index.php index.html

The php.ini and php-fpm.ini file can be found in:
    /usr/local/etc/php/7.1/

php@7.1 is keg-only, which means it was not symlinked into /usr/local,
because this is an alternate version of another formula.

If you need to have php@7.1 first in your PATH run:
  echo 'export PATH="/usr/local/opt/php@7.1/bin:$PATH"' >> ~/.bash_profile
  echo 'export PATH="/usr/local/opt/php@7.1/sbin:$PATH"' >> ~/.bash_profile

For compilers to find php@7.1 you may need to set:
  export LDFLAGS="-L/usr/local/opt/php@7.1/lib"
  export CPPFLAGS="-I/usr/local/opt/php@7.1/include"


To have launchd start php@7.1 now and restart at login:
  brew services start php@7.1
Or, if you don't want/need a background service you can just run:
  php-fpm
==> Summary
🍺  /usr/local/Cellar/php@7.1/7.1.26: 508 files, 63MB, built in 5 minutes 46 seconds
==> Caveats
==> php@7.1
To enable PHP in Apache add the following to httpd.conf and restart Apache:
    LoadModule php7_module /usr/local/opt/php@7.1/lib/httpd/modules/libphp7.so

    <FilesMatch \.php$>
        SetHandler application/x-httpd-php
    </FilesMatch>

Finally, check DirectoryIndex includes index.php
    DirectoryIndex index.php index.html

The php.ini and php-fpm.ini file can be found in:
    /usr/local/etc/php/7.1/

php@7.1 is keg-only, which means it was not symlinked into /usr/local,
because this is an alternate version of another formula.

If you need to have php@7.1 first in your PATH run:
  echo 'export PATH="/usr/local/opt/php@7.1/bin:$PATH"' >> ~/.bash_profile
  echo 'export PATH="/usr/local/opt/php@7.1/sbin:$PATH"' >> ~/.bash_profile

For compilers to find php@7.1 you may need to set:
  export LDFLAGS="-L/usr/local/opt/php@7.1/lib"
  export CPPFLAGS="-I/usr/local/opt/php@7.1/include"


To have launchd start php@7.1 now and restart at login:
  brew services start php@7.1
Or, if you don't want/need a background service you can just run:
  php-fpm
```

主要查看后面的提示信息并配置环境
``` bash
$ echo 'export PATH="/usr/local/opt/php@7.1/bin:$PATH"' >> ~/.bash_profile
$ echo 'export PATH="/usr/local/opt/php@7.1/sbin:$PATH"' >> ~/.bash_profile
```
然后`source ~/.bash_profire`

再次重新运行thinkphp中的验证码模块，实验成功

# 参考资料

https://blog.csdn.net/leiflyy/article/details/53016769

https://blog.csdn.net/liaobangxiang/article/details/79460290

https://github.com/EricLi404/notes/issues/1

https://stackoverflow.com/questions/50259893/home-brew-php-7-2-5-install-with-curl

https://hackycy.github.io/2019/03/19/Mac-OS-X-10-11-Rootless-介绍







