---
title: Android返回不销毁Activity，直接进入后台
date: 2019-07-03 10:20:55
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

最近打开闲鱼，微信，发现他们点击返回到桌面的时候，他们的activity并没有直接销毁，还是依然保留在返回之前浏览的界面。查了一下资料，的确有可以操作的办法。

<!-- more -->

# isTaskRoot()

`isTaskRoot()`方法用来判断该Activity是否为任务栈中的根Activity，即启动应用的第一个Activity。

# moveTaskToBack()

`moveTaskToBack()`方法用于将activity退到后台，而不是直接finish掉。

从生命周期来说，会执行`onPause`、`onStop`，但不会执行`onDestroy` 。恢复的时候也一样，会执行`onStart`、`onResume`，但不会执行`onCreate`。

参数nonRoot表示的含义是此方法对非根activity是否有效：

- true表示对所有activity均有效，
- false表示只对根activity有效。

返回值：该activity被退出到后台或者他已经在后台了返回true，否则返回false

# 官方文档：

> public boolean moveTaskToBack (boolean nonRoot) 
> Since: API Level 1Move the task containing this activity to the back of the activity stack. The activity’s order within the task is unchanged. 
> Parameters: 
> nonRoot If false then this only works if the activity is the root of a task; if true it will work for any activity in a task. 
> Returns: 
> If the task was moved (or it was already at the back) true is returned, else false.

# 使用案例

重写`onBackPressed`或者`onKeyDown`事件等来监听返回键事件：

``` java
@Override
 public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (event.getKeyCode() == KeyEvent.KEYCODE_BACK) {
            if (isTaskRoot()) {
                moveTaskToBack(false);
                return true;
            } else {
                return super.onKeyDown(keyCode, event);
            }
        } 
 }
```

# 参考资料

http://www.voidcn.com/article/p-wbosovty-bru.html

http://www.voidcn.com/article/p-poscpyhl-bn.html

