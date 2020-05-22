---
title: VirtualBox中解决安装CentOS7后无法上网问题
date: 2020-05-22 13:41:06
keywords:
    - VirtualBox
tags:
    - Centos7
    - 环境搭建
categories:
    - DBA
---

# 前言

偶尔需要搭建一些测试用的环境时使用虚拟机无疑是一个很好的方法。但是在安装玩Centos 7系统后网络的问题是一直困扰的。以防忘记先记录下来。

<!-- more -->

环境：Mac + VirtualBox

# 配置虚拟机的网卡

按照下图配置虚拟机的网络

![image](https://user-images.githubusercontent.com/26972260/82634776-400ac680-9c31-11ea-86d2-1ca66c615e5a.png)

其中，连接方式选择桥接网卡、界面名称选择你主机上网用的无线网卡、控制芯片选择桌面、混杂模式选择拒绝、勾选接入网络。

> 配置前请先停止虚拟机

# 配置CentOS7的网络

修改网卡对应的配置文件，设置`BOOTPROTO`为`dhcp`，`ONBOOT`为`yes`

``` bash
$ vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
```

![image](https://user-images.githubusercontent.com/26972260/82634922-9d9f1300-9c31-11ea-98f7-5ad6dd5ac3bf.png)

# 重启网络服务

``` bash
$ service network restart
```

配置完成后重启网络，测试ping通即可。

# 参考资料

https://blog.csdn.net/scaleqiao/article/details/44206825