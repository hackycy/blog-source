---
title: Android TextView更换字体
date: 2019-08-07 14:52:47
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

在开发UI时有时会涉及到TextView设置字体样式，TextView中在xml下仅可以设置自带的字体样式：

<!-- more -->

``` xml
android:typeface="monospace"
```

自带有四种文字类型，分别是：

- monospace
- normal
- sans
- serif

看看运行效果后的样式：

![](systtf.png)

有时候UI需要设定特定字体的时候，我们可以给在代码中TextView设置特定的字体。

比如我们随便下载一些字体：https://www.sentyfont.com/download.htm

放在Assets目录下：

![](demo1.png)

> 注意留意目录结构，demo将字体文件放在了fonts目录下

然后在代码中设置：

``` java
TextView tv = findViewById(R.id.tv);
Typeface tf = Typeface.createFromAsset(this.getAssets(), "fonts/HanyiSentyVimalkirti.ttf");
tv.setTypeface(tf);
```

运行效果：

![](demo2.png)

> 字体文件otf等都支持。
>
> 字体包过大可以将使用到的字提取出来，不用将所有字体都打包成一个ttf。