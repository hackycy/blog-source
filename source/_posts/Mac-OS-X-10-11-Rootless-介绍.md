---
title: Mac OS X 10.11+ Rootless 介绍
date: 2019-03-19 10:31:05
tags:
    - Mac
categories:
    - 程序人生
keywords:
    - 小技巧
---

# Rootless

Mac OS X 10.11+ (El Capitan) 以后，引入了 Rootless 安全机制，该机制限制了 root 用户的权限，有部分操作即使是 root 也无法执行。
在搭建php环境时遇到了这个，所以记录了下来。

<!-- more -->

>该机制的详细介绍，请参考：
>Wikipedia: System Integrity Protection 
>Apple: System Integrity Protection Guide

# 主要限制有：

以下目录无法修改：
/System, /bin, /sbin, 或者 /usr (/usr/local 除外)，以及内置 App 和系统工具 Utilities。
具体的限制名单在 /System/Library/Sandbox/rootless.conf 文件中有定义，该文件行首的 * 表示该行记录不在限制之列
无法追踪调试系统进程
无法加载未经验证的内核扩展

# 可能引起的问题和解决方案

- 修改受限制的文件或文件夹
``` bash
sudo cp -f FILE /usr/bin/

# 报错： cp: /usr/bin/FILE: Operation not permitted
```

- 执行部分命令受挫
``` bash
sudo gem install posix-spawn -v '0.3.11'

# 报错：
Building native extensions.  This could take a while...
ERROR:  While executing gem ... (Errno::EPERM)
    Operation not permitted - /usr/bin/posix-spawn-benchmark
```

# 关闭/开启Rootless

如果遇到的问题难以解决，也可以关闭 rootless 功能，彻底解决引起的权限问题，但是关闭 rootless 将会严重降低系统安全性，必须尽快重新开启。

## 关闭

1、重启 Mac 并按住 Command+R，进入恢复模式
2、打开终端 Terminal
3、输入`csrutil disable`

## 开启

1、重启 Mac 并按住 Command+R，进入恢复模式
2、打开终端 Terminal
3、输入`csrutil enable`

