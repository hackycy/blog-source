---
title: Jenkins更换语言
date: 2019-12-04 10:47:49
tags:
    - Jenkins
categories:
    - Jenkins
---

最近学习Jenkins中，使用Docker安装完成后，Jenkins的默认语言是狗血的繁体版本，想更换成英文版本或者中文版本。

<!-- more -->

# 更换Google浏览器默认语言

我google之前设置的默认语言第一位是中文繁体版本，我现在直接删除掉了。后面Jenkins就更换回了英文版本。

![](changegooglelan.png)

# 使用Locale插件

繁体版本下找到插件为`管理Jenkins`->`外掛程式管理`

![](findplugin.png)

在可用的插件列表搜索Locale插件，选择直接安装。

![](findplugin2.png)

安装完成后，进入`管理Jenkins`->`设定系统`

![](setting.png)

应用并保存即可。

> 记住Ignore browser preference and force this language to all users这个单选框要勾选上，否则不生效

# 总结

两种方式中，第二种中文好像并不是完全支持，有些中文有些英文。

所以我选择了使用第一种方式。学习的情况下还是多些习惯英文吧。