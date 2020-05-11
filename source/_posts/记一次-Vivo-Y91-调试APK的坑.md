---
title: 记一次 Vivo Y91 调试APK的坑
date: 2020-04-14 14:47:46
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

# 原因

在项目工作测试途中，发现Vivo Y91的机型却一直出现debug安装运行问题

<!-- more -->

# 解决

1.设置Android Studio，关闭`Instant Run`。`File`->`Settings`->`BUild,Execution…`->`Instant Run` , 关闭勾选`Enable Instant Run`
2.在你的gradle.properties文件添加一句:

``` properties
android.injected.testOnly = false
```

## 其他解决方法（未测试）

根据每个手机不同，未知也不太一样，比如 vivo x21 是在：设置 -> 更多设置 -> 未知xx管理（记不太清楚了）。

一加3T前段时间更新了 Android 8.0 ，它的位置在： 设置 -> 应用程序 -> 特殊访问权限 -> 安装未知应用。如下图，打开对应的软件即可。

# 解释

关于开发过程中是否开启 Instant Run，我个人建议还是关闭它，我在开发过程中一直都是关闭着的，因为之前开启它，出现了一些莫名其妙的问题，目前我们的神器 Android Studio 已经优化的很好了，即使重新打包，也浪费不了多少时间。

关于`android.injected.testOnly = false`的设置，那是因为我们跑的 run apk都是 debug 版本，也就是测试版本，而 vivo 的一些就不支持这个测试apk（网上有人这样说，经过验证，不假，在找答案的过程中，看到过很多吐槽：vivo x21、Y91不适合做测试机，因为它只认正式包）