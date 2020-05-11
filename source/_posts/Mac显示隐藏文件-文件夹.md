---
title: Mac显示隐藏文件/文件夹
date: 2019-03-08 11:50:02
tags:
    - Mac
categories:
    - 程序人生
keywords:
    - 小技巧
---

*Mac下一些隐藏文件夹是没有办法可见的，终端下也不会进行显示，例如用户文件下下的.m2*

<!-- more -->

# 终端执行命令

## 设置隐藏文件可见

``` bash
$ defaults write com.apple.finder AppleShowAllFiles TRUE
```

## 设置隐藏文件不可见

``` bash
$ defaults write com.apple.finder AppleShowAllFiles FALSE 
```

# 重启Finder生效

``` bash
$ killall Finder
```
