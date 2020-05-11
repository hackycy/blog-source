---
title: Xcode报错修复SWIFT_VERSION '3.0' is unsupported, supported versions are:4.0,4.2,5.0
date: 2019-07-13 17:01:35
keywords:
    - Ios
    - Xcode 10
tags:
    - Ios
categories:
    - Ios
---

**Xcode版本：10.2.1**

今天导入了一个Demo项目，出现了SWIFT_VERSION '3.0' is unsupported, supported versions are: 4.0, 4.2, 5.0.错误，记录下解决办法。

<!-- more -->

![](errorpic.png)

![](errorpic2.png)

**解决方法：**

找到发生错误的Target：

![](fix1.png)

![](fix2.png)

步骤： `${Target}` >`Build Settings` > `Swift Compiler - Language` > `Swift Language Version`选择支持的版本即可。

![](fix3.png)

# 参考资料

https://www.jianshu.com/p/bdfcb8759dee