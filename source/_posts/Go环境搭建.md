---
title: Go环境搭建
date: 2018-09-22 14:45:59
tags:
    - Golang基础
    - 环境搭建
categories:
    - Golang
---

# Go语言优势
GO语言（Golang）是云计算时代的C语言。它不仅有着更高的生产效率，也对多处理器系统应用程序的编程进行了优化，语言层面支持并发，内置runtime，支持GC，并且语法简单易学，并不像java有着那么复杂的语法。

<!-- more -->

# Go可以做什么
- 服务器编程
- 分布式系统
- 网络编程
- 内存数据库
- 云平台
- and so on

# 环境搭建
教程基于win10，linux等系统请参考其他文章
[官网](https://golang.org/)，自行下载msi文件，傻瓜式安装，安装路径不要出现中文避免出差错。
需要翻墙，没有可以选择[Golang中文网](https://studygolang.com)
测试安装：打开终端输入
``` bash
$ go version
go version go1.11 windows/amd64
```
即可完成安装

# 环境配置
打开终端输入
``` bash
$ go env
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\Administrator\AppData\Local\go-build
set GOEXE=.exe
set GOFLAGS=
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GOOS=windows
set GOPATH=E:\Go
set GOPROXY=
set GORACE=
set GOROOT=G:\Golang
set GOTMPDIR=
set GOTOOLDIR=G:\Golang\pkg\tool\windows_amd64
set GCCGO=gccgo
set CC=gcc
set CXX=g++
set CGO_ENABLED=1
set GOMOD=
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-m64 -mthreads -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=C:\Users\ADMINI~1\AppData\Local\Temp\go-build669911038=/tmp/go-build -gno-record-gcc-switches
```

msi包基本会把环境配置完成，但要配置GOPATH目录，不要用默认配置的
计算机(右键)-属性-高级系统设置-环境变量
在 ** 用户变量 ** 下添加 __ GOPATH __
值为你想要的路径即可。在配置目录下创建三个文件夹，分别是
- src目录
    此目录是自己在GoPath目录下手动创建的，用于放源代码文件。
- pkg目录
    此目录是在src的源文件目录下对.go文件通过go install之后自动生成的，放编译后的包文件。
- bin目录
    此目录是在src的源文件目录下对 .go文件通过go build和go install之后自动生成的，.go文件要调用pkg下的包文件。

环境基本搭建完成。
再次输入
``` bash
$ go env
```
发现修改了即可。
