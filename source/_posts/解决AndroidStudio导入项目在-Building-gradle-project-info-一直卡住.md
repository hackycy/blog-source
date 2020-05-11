---
title: 解决AndroidStudio导入项目在 Building gradle project info 一直卡住
date: 2019-02-28 15:14:02
keywords:
    - Android
tags:
    - Android
    - Android Studio
categories:
    - Android
---

** Android studio一个小小的痛点 **

<!-- more -->

# 解决AndroidStudio导入项目在 Building gradle project info 一直卡住
Android Studio导入项目的时候，一直卡在Building gradle project info这一步，主要原因还是因为被墙的结果。gradle官网虽然可以访问，但是速度连蜗牛都赶不上...

## 三种解决办法

### 离线包下载导入方式

查看所需gradle版本：打开`C:\Users\用户名\.gradle\wrapper\dists\gradle-x.xx-all\xxxxxxxxxxxx`，
如果里面的gradle-xx-all.zip不完整（如0KB），则说明下载不成功，需要下载离线包放置到该目录下。

下载地址：[https://services.gradle.org/distributions/](http://note.youdao.com/)

### 修改gradle-wrapper.properties方式

1、随便找一个你之前能够运行的AS项目

2、打开项目的`/gradle/wrapper/gradle-wrapper.properties`文件

3、复制最后一行distributionUrl这一整行的内容，例如：`distributionUrl=https\://services.gradle.org/distributions/`gradle-2.8-all.zip，替换到你要导入的项目里的gradle-wrapper.properties文件中。

4、重启Android Studio，重新导入项目就可以了~~

### 翻墙
翻开中国伟大的墙就可以了。本人一直用的蓝灯。

