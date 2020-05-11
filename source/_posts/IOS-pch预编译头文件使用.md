---
title: IOS pch预编译头文件使用
date: 2019-04-08 12:19:05
keywords:
    - Ios
    - pch
    - NSLog
tags:
    - Ios
categories:
    - Ios
---

# PCH是什么

PCH文件是一个标准的预编译头文件( Pre-Compiled Header)，在Xcode6之前的版本中，系统模板会在Supporting Files文件夹自动创建。但在Xcode6之后的版本中取消了这一文件，如果我们需要使用pch文件，则需要手动创建。去掉的主要的原因:

<!-- more -->

>1、去掉自动导入的系统框架类库的头文件，可以提高原文件的复用性，便于迁移。
>2、一个体积大的Prefix Header会大大增加编译时间。

再来看看pch的作用：

>1.存放一些全局的宏(整个项目中都用得上的宏)。
>2.用来包含一些头文件(整个项目中都用得上的头文件)。
>3.能自动打开或者关闭日志输出功能。

如果你的pch文件确实很大，那那肯定影响编译速度，苹果去掉他可能是要加快编译时间增加用户体验。虽然失去了编程的便利性。事实上，正确运用pch文件时预编译后的头文件会被缓存起来，再次编译的时候就不需要重新编译pch文件中导入的内容，编译速度并不会降低很多。很重要的一点就是pch文件确实给我们编程带来便利，我们不用在每个文件内重复引用另一个文件；那怎样才能提高编译速度呢？降低编译速度的罪魁祸首就是大量的共用性不高的宏定义和头文件的引入。编译的时候整个工程范围地查找和替换这些宏定义字段，重复导入这些头文件，不慢就奇怪了。

# PCH创建

## 创建

![PCH文件创建](create_pch.png)

点击创建，输入文件名即可。

## 配置

在项目->`Build Settings`下

- 找到该`Precompile Prefix Header`项设置为`YES`,这样的话pch会被预编译，预编译后的pch文件会被缓存起来，从而提高编译速度。当 `Precompile Prefix Header` 为`NO`时，那么pch不会被预编译，而是在每一个用到它导入的框架类库的`.m`文件中编译一次

- 找到`Prefix Header`项

![](pch_config.png)

双击可弹出输入框，给Prefix Header设置路径，只需要点击pch文件然后按住鼠标左键拖过来就行，但是`/Users/zjyzy/WorkPlace/xcode/AllDemo/BarDemo/BarDemo/PrefixHeader.pch`代表的是绝对路径，当用别的电脑时就不能识别了，这时就可以用到`$(SRCROOT)`来替换，在iOS中`$(SRCROOT)`代表的是项目根目录下，路径形式为：
`$(SRCROOT)/当前工程名字/需要包含头文件所在文件夹`
所以把路径改为:
`$(SRCROOT)/BarDemo/PrefixHeader.pch`

# PCH使用

一般来说在你向pch添加全局的头文件之前,必须添加以下代码：

``` c
#ifdef __OBJC__

...

#endif
```

这个宏定义的作用是保证只有oc文件可以调用pch里面的头文件，一些非OC语言不能调用，比如.cpp,.mm。如果不加入，那么如果代码中带有.cpp,.mm文件，那么将报错。NSObjCRuntime.h  NSObject.h  NSZone.h将会报出编译异常。
这样你就可以在pch文件当中添加一些常用头文件、宏定义了。

## 宏来自定义只能在debug环境下使用输出日志

在pch文件下添加如下代码

``` c
#ifdef __OBJC__

#ifdef DEBUG
# define TTLog(...) NSLog(__VA_ARGS__);
#else
# define TTLog(...);
#endif

#endif
```

>`TTLog`为自定义名称,在项目中输出则将`NSLog`替换为`TTLog`即可。

**宏定义的debug /release切换，见下图操作步骤**

![](rd.png)

在选择了`Edit Scheme`后`run info`中切换`debug/release`模式

![](rd2.png)


# 参考

https://my.oschina.net/goboy/blog/1838003

https://www.jianshu.com/p/a1d61f5cc454
