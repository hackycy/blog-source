---
title: IOS模拟器截图/录制屏幕
date: 2019-03-06 11:59:19
keywords:
    - Ios
    - 模拟器
    - 截图，录制屏幕
tags:
    - Ios
categories:
    - Ios
---

# 截图

<!-- more -->

iOS 的模拟器截图，按 `Command + S`或者通过 `File` 菜单都可以完成。

# 录制屏幕

终端下运行该命令，

``` bash
xcrun simctl io booted recordVideo <filename>.<extension>
```

运行命令后没有报错则已经开始录制屏幕了，如果想要停止则直接`Ctrl + C` 停止结束命令即可。默认视频会保存在终端的当前目录下，当然你也可以指定当前目录。

## xcrun unable to find simctl解决方法

如果出现命令无法执行并报出这个错误
`xcrun: error: unable to find utility "simctl", not a developer tool or in PATH`

修改一下打开`Xcode` > `Preferences` > `Locations` 更改一下 `Command Line Tools`选项就可以了,如图

![](setting.png)



