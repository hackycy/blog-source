---
title: >-
  解决Android Studio——Configuration on demand is not supported by the current
  version of the Android Gradle
date: 2020-01-17 14:45:50
keywords:
    - Android
tags:
    - Android
    - Android Studio
categories:
    - Android
---

【错误】
Configuration on demand is not supported by the current version of the Android Gradle plugin since you are using Gradle version 4.6 or above. 

Suggestion: disable configuration on demand by setting org.gradle.configureondemand=false in your gradle.properties file or use a Gradle version less than 4.6.

【翻译】
由于使用你正在使用 Gradle 版本4.6或以上，当前版本的Android的 Gradle 插件不支持按需配置。

建议：通过在你的 gradle.properties 文件中设置 org.gradle.configureondemand=false 禁用按需配置，或者使用一个低于4.6版本的 Gradle。

<!-- more -->

**解决方案1：降级**

打开 **gradle-wrapper.properties** 文件，修改 **distributionUrl** 参数，将其后面修改为低于4.6版本的 Gradle。如：

``` properties
distributionUrl=https\://services.gradle.org/distributions/gradle-4.1-all.zip
```

![](properties.png)

**解决方法2：禁用按需配置**

1、打开 **gradle.properties** 文件，共有两个：**Global Properties** 和 **Project Properties**，将其中的

``` properties
org.gradle.configureondemand=true
```

修改为

``` properties
org.gradle.configureondemand=false
```

> 或删除该语句，或注释掉该语句。

2、或者通过 **Preferences** 菜单，找到 **Build, Execution, Deployment** 里面的 **Compiler**，将右面的 **Configure on demand** 取消勾选。

![](panel.png)

**参考链接**

https://stackoverflow.com/questions/49990933/configuration-on-demand-is-not-supported-by-the-current-version-of-the-android-g