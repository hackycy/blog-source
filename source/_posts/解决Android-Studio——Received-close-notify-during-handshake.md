---
title: 解决Android Studio——Received close_notify during handshake
date: 2020-02-18 12:24:13
keywords:
    - Android
tags:
    - Android
    - Android Studio
categories:
    - Android
---

**问题ERROR ： Received close_notify during handshake**

``` log
* What went wrong:
A problem occurred configuring root project 'NotificationDemo'.
> Could not resolve all artifacts for configuration ':classpath'.
   > Could not download guava.jar (com.google.guava:guava:23.0)
      > Could not get resource 'https://jcenter.bintray.com/com/google/guava/guava/23.0/guava-23.0.jar'.
         > Could not GET 'https://jcenter.bintray.com/com/google/guava/guava/23.0/guava-23.0.jar'.
            > Received close_notify during handshake
   > Could not download kotlin-reflect.jar (org.jetbrains.kotlin:kotlin-reflect:1.2.0)
      > Could not get resource 'https://jcenter.bintray.com/org/jetbrains/kotlin/kotlin-reflect/1.2.0/kotlin-reflect-1.2.0.jar'.
         > Could not GET 'https://jcenter.bintray.com/org/jetbrains/kotlin/kotlin-reflect/1.2.0/kotlin-reflect-1.2.0.jar'.
            > Received close_notify during handshake
```

<!-- more -->

可能是Jcenter一直连接超时了吧，请求不了，所以查了一下资料，暂时使用国内源即可。

> 有翻墙应该不会出现该问题，但是最近蓝灯好像又连不上了，真是麻鬼烦的玩意。

**解决方法**

在项目的 Gradle 文件里，配置国内源（将 `Jcenter()`注掉或者删掉，替换成下图两行`maven`配置）

``` gradle
maven{url 'http://maven.aliyun.com/nexus/content/groups/public/'}
maven{url "https://jitpack.io"}
```

重新 `Sync` 或者 `Rebuild`即可

# 参考资料

https://blog.csdn.net/w13576267399/article/details/82019359