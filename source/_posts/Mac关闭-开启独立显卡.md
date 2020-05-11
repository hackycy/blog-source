---
title: Mac关闭/开启独立显卡
date: 2019-03-19 17:19:24
tags:
    - Mac
categories:
    - 程序人生
---

本人使用的是2018款15寸的Mbp，每次用迅雷播放器观看总是自动帮我切换到独立显卡，功耗和发热实在是太严重了，所以想单独仅使用集成显卡。

<!-- more -->

普通的界面中只找到了自动切换模式在`设置`-`节能`选项卡中可以找到。

![](autoswitch.png)

而想单独使用，则终端使用命令
``` bash
#强制使用核显(集成显卡)
$ sudo pmset -a GPUSwitch 0
#强制使用独立显卡
$ sudo pmset -a GPUSwitch 1
#自动切换
$ sudo pmset -a GPUSwitch 2
```

自由选择切换即可。

或者使用一个`gfxCardStatus`软件也可实现，但未经尝试。命令更方便。
