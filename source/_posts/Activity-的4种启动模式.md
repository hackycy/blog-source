---
title: Activity 的4种启动模式
date: 2019-06-20 11:27:34
tags:
    - Android
categories:
    - Android
---

启动模式简单地说就是Activity启动时的策略，在`AndroidManifest.xml`中的标签的`android:launchMode`属性设置；启动模式有4种，分别为standard、singleTop、singleTask、singleInstance；

<!-- more -->

- standard 标准模式，每次都新建一个实例对象
- singleTop 如果在任务栈顶发现了相同的实例则重用，否则新建并压入栈顶
- singleTask 如果在任务栈中发现了相同的实例，将其上面的任务终止并移除，重用该实例。否则新建实例并入栈
- singleInstance 允许不同应用，进程线程等共用一个实例，无论从何应用调用该实例都重用

# 任务栈

每个应用都有一个任务栈，是用来存放Activity的，功能类似于函数调用的栈，先后顺序代表了Activity的出现顺序；比如Activity1-->Activity2-->Activity3,则任务栈为：

![](taskstack.gif)

# 启动模式

## standard

![](standard.gif)

每次激活Activity时(startActivity)，都创建Activity实例，并放入任务栈；

但是每次都新建一个实例的话真是过于浪费，为了优化应该尽量考虑余下三种方式。

## singleTop

![](singleTop.gif)

每次扫描栈顶，如果在任务栈顶发现了相同的实例则重用，否则新建并压入栈顶。

在`AndroidManifest.xml`中

``` xml
<activity
    android:name=".SingleTopActivity"
    android:label="@string/singletop"
    android:launchMode="singleTop" >
</activity>
```

##  singleTask

![](singleTask.gif)

与singleTop的区别是singleTask会扫描整个任务栈并制定策略。上效果图：

使用时需要小心因为会将之前入栈的实例之上的实例全部移除，需要格外小心逻辑。

在`AndroidManifest.xml`中

``` xml
<activity
    android:name=".SingleTopActivity"
    android:label="@string/singletop"
    android:launchMode="singleTop" >
</activity>
```

## singleInstance

![](singleInstance.gif)

这个的理解可以这么看：在微信里点击“用浏览器打开”一个朋友圈，然后切到QQ再用浏览器开一个网页，再跑到哪里再开一个页面。每次我们都在Activity中试图启动另一个浏览器Activity，但是在浏览器端看来，都是调用了同一个自己。因为使用了singleInstance模式，不同应用调用的Activity实际上是共享的。

在`AndroidManifest.xml`中

``` xml
<activity
    android:name=".SingleTopActivity"
    android:label="@string/singletop"
    android:launchMode="singleTop" >
</activity>
```

# 参考链接

- [http://www.cnblogs.com/fanchangfa/archive/2012/08/25/2657012.html](https://hit-alibaba.github.io/interview/Android/basic/Android中Activity启动模式详解)
- [Android入门：Activity四种启动模式](http://www.cnblogs.com/meizixiong/archive/2013/07/03/3170591.html)