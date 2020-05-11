---
title: >-
  解决Android Studio——No toolchains found in the NDK toolchains folder for ABI
  with prefix: mips64el-linux-android错误
date: 2019-07-12 11:11:34
keywords:
    - Android
tags:
    - Android
    - Android Studio
categories:
    - Android
---

今天导入了一个比较老一些的项目，AS一直提示出该错误：

``` bash
No toolchains found in the NDK toolchains folder for ABI with prefix: mips64el-linux-android
```

<!-- more -->

查了网上一些解决办法，可用的办法是该办法：

**解决方案1：**

1. 修改build.gradle中的Gradle Build Tool版本，改为3.1以及以上版本
2. 将Android Studio升级到3.1以及以上版本

``` groovy
dependencies {
    classpath 'com.android.tools.build:gradle:3.2.0'
}
```

**问题原因：**

NDK的更新记录里有一段话：

>This version of the NDK is incompatible with the Android Gradle plugin
>version 3.0 or older. If you see an error like
>`No toolchains found in the NDK toolchains folder for ABI with prefix: mips64el-linux-android`,
>update your project file to [use plugin version 3.1 or newer]. You will also
>need to upgrade to Android Studio 3.1 or newer.

即新版本的NDK与3.0及以前旧版的Android Gradle plugin插件不兼容

**解决方案2**

找到NDK的配置目录`File`->`Project Structure`->`SDK Location`

![](ndklocaltion.png)

打开命令行，进入到Android NDK Location目录

``` bash
$ cd /Users/zjyzy/Library/Android/sdk/ndk-bundle
```

在进入到toolchains目录

``` bash
$ cd toolchains
```

输入，即配置软连接

``` bash
ln -sf aarch64-linux-android-4.9 mips64el-linux-android 
```

> 或者ln -s arm-linux-androideabi-4.9 mipsel-linux-android

即可解决。

> 删除则直接使用命令`rm -rf mips64el-linux-android`

**解决方案3**

去Android官网下载，方案未尝试。

放个别人的链接：https://blog.csdn.net/qq_24118527/article/details/82867864

**解决方案4**

> 升级到Android Studio 3.1.2版本后，原有的NDK由r16升级到r17，因为r17不再支持mips，导致`ndk-bundle/toolchains/mips64el-linux-android-4.9/prebuilt/linux-x86_64/bin/mips64el-linux-android-strip` 找不到, 导致编译报：**Error:org.gradle.process.internal.ExecException: A problem occurred starting process 'command '...\ndk-bundle\toolchains\aarch64-linux-android-4.9\prebuilt\windows-x86_64\bin\aarch64-linux-android-strip''**。

网上找到的解决办法是删掉NDK或者降级使用r16的NDK，这其实是治标不治本的，这里的解决办法是在`build.gradle`脚本里排除mips

```  gradle

android {
    defaultConfig {
        ndk {
            //支持的CPU架构，如armeabi、x86、mips等
            abiFilters "armeabi", "x86"
        }
    }
    packagingOptions {
        doNotStrip '*/mips/*.so'
        doNotStrip '*/mips64/*.so'
    }
}
```

参阅：https://stackoverflow.com/questions/42739916/aarch64-linux-android-strip-file-missing

**总结**

其实主要是因为项目中NDK17及以上版本和NDK16版本不再兼容了，如果旧项目编译不通过并且还需要支持mips可以重新再下载一次NDK16的环境。