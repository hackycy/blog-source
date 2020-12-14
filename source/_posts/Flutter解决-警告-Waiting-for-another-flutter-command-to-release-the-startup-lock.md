---
title: Flutter解决 警告 Waiting for another flutter command to release the startup lock
date: 2020-12-14 09:49:26
tags:
    - Flutter
categories:
    - Flutter
---

**运行flutter命令的时候显示出如下警告时**

```bash
Waiting for another flutter command to release the startup lock
```

当项目异常关闭，或者`android studio`用任务管理器强制关闭，下次启动就会出现上面的一行话。

此时需要打开`${flutter的安装目录}/bin/cache/lockfile`，删除就行了。

或者直接用下面的命令：`rm -rf ${flutter的安装目录}/bin/cache/lockfile`

