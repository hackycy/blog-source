---
title: Xcode报错修复Multiple commands produce
date: 2019-07-13 17:17:38
keywords:
    - Ios
    - Xcode 10
tags:
    - Ios
categories:
    - Ios
---

**Xcode版本：10.2.1**

今天导入了一个Demo项目，出现了**Multiple commands produce**错误，记录下解决办法。

<!-- more -->

Xcode10使用了一个的新创建系统，比之前的提供更好的可靠性与创建性能，而且可以获取项目配置问题（默认设置新创建系统）

>Build System
>
>Again, Xcode 10 uses a new build system. The new build system provides improved reliability and build performance, and it catches project configuration problems that the legacy build system does not.
> The legacy build system is still available in Xcode 10. To use the legacy build system, select it in the File > Project/Workspace Settings sheet. Projects configured to use the legacy build system will display an orange hammer icon in the Activity View.

在苹果文档中，提及Xcode10中的关于旧项目New Build System更改适配中提及到以下两点

>The new build system has stricter checks for cycles between elements in the build in order to prevent unnecessary rebuilds.
>
>It is an error for any individual file in the build to be produced by more than one build command. For example, if two targets each declare the same output file from a shell script phase, factor out the declaration of the output file into a single target.

New Build System会对构建中的元素循环进行严格的检查，避免不必要的重建，这个也是错误出现的原因。

**错误信息**

``` bash
error: Multiple commands produce '/Users/zjyzy/Library/Developer/Xcode/DerivedData/UMengComDemo-eisszriydfvwtlgnymkievxxjndx/Build/Products/Debug-iphonesimulator/UMengComDemo.app/Info.plist':
1) Target 'UMengComDemo' (project 'UMengComDemo') has copy command from '/Users/zjyzy/WorkPlace/xcode/AllDemo/test/MultiFunctioniOSDemo/UMengComDemo/Info.plist' to '/Users/zjyzy/Library/Developer/Xcode/DerivedData/UMengComDemo-eisszriydfvwtlgnymkievxxjndx/Build/Products/Debug-iphonesimulator/UMengComDemo.app/Info.plist'
2) Target 'UMengComDemo' (project 'UMengComDemo') has process command with output '/Users/zjyzy/Library/Developer/Xcode/DerivedData/UMengComDemo-eisszriydfvwtlgnymkievxxjndx/Build/Products/Debug-iphonesimulator/UMengComDemo.app/Info.plist'
```

**解决方案**

- 不使用`New Build System`

  打开`Xcode` > `File` > `Project Setting`

  ![](fix1.png)

  将`Build System`选项选择为`Legacy Build System`即可。

- 根据出错信息，在新创建系统模式下，去除多余的引用重建

  ![](fix2.png)

  在` target` -> `Build phase` > `Copy Bundle Resource` 中找到`info.plist`，移除掉`info.plist`即可。

# 参考资料

https://www.jianshu.com/p/fdb1421f3c8b