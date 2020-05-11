---
title: Android无法获取本地服务器(Localhost)解决方案
date: 2019-03-10 23:09:36
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

在Android开发中通过localhost或127.0.0.1访问本地服务器时，会报java.net.ConnectException: localhost/127.0.0.1:8083 -Connection refused异常。

<!-- more -->

为什么会报这个异常呢？因为Android模拟器本身把自己当做了localhost或127.0.0.1，而此时我们又通过localhost或127.0.0.1访问本地服务器，所以会抛出异常了。

解决方案：

使用`10.0.2.2`来代替`localhost`和`127.0.0.1`即可
