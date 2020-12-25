---
title: 解决Mac出现Kotlin could not find the required JDK tools in the Java installation
date: 2020-12-25 18:17:38
keywords:
    - Android
tags:
    - Android
    - Android Studio
categories:
    - Android
---

``` bash
> Task :compileKotlin FAILED

FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':compileKotlin'.
> Kotlin could not find the require JDK tools in the Java installtion '/Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home' used by Gradle. Make sure is running on a JDK, not JRE.
```

在混编开发过程中，打包的时候出现了该问题，排查很久，总算找到了原因：**没有配置JAVA_HOME环境变量**。

<!-- more -->

**解决方案**

安装好JDK后，获取JAVA的安装路径

``` bash
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (2):
    1.8.201.09 (x86_64) "Oracle Corporation" - "Java" /Library/Internet Plug-Ins/JavaAppletPlugin.plugin/Contents/Home
    1.8.0_201 (x86_64) "Oracle Corporation" - "Java SE 8" /Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
```

mac下编辑profile：

``` bash
$ vi ~/.bash_profile

# android
export ANDROID_HOME=/Users/zjyzy/Library/Android/sdk
export PATH=${PATH}:${ANDROID_HOME}/tools
export PATH=${PATH}:${ANDROID_HOME}/platform-tools

# java
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
export CLASSPAHT=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH:
```

编辑完成后，按`esc`退出插入模式，输入`:wq`退出保存即可。

``` bash
$ source ~/.bash_profile
```

生效后验证：

``` bash
$ echo $JAVA_HOME
/Library/Java/JavaVirtualMachines/jdk1.8.0_201.jdk/Contents/Home
```

问题解决。