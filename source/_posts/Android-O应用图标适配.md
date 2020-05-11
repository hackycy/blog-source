---
title: Android O应用图标适配
date: 2019-07-13 10:58:52
keywords:
    - Android
    - 图标适配
    - Android O
tags:
    - Android
categories:
    - Android
---

Android 8.0现在已经很普及了，甚至有的厂商已经开始升级到Android P，至少我的三星上个星期也是7月初就开始推送Android P让我更新自己的手机系统了，那么应用图标的适配就肯定是板上钉钉的事情了。

<!-- more -->

# 前景

**手机应用图标的历史**：其实要从苹果开始讲起。在上世纪80年代，苹果还在设计Lisa和Macintosh电脑的时候，乔布斯就是个圆角矩形的狂热支持者。当时苹果的工程师写出了一套绝妙的算法，可以在电脑上绘制出圆和椭圆，所有观看者都被震惊了，除了乔布斯，因为乔布斯觉得圆和椭圆虽然也不错，但是如果能绘制出带圆角的矩形就更好了。当时那位工程师觉得这是不可能实现的，而且也完全用不着圆角矩形，能满足基本的绘图需求就可以了。乔布斯愤怒地拉着他走了3条街，指出大街上各种应用圆角矩形的例子，最后那位工程师第二天就做出了绘制圆角矩形的功能。

因此，在2007年一代iPhone诞生的时候，所有应用程序的图标都毫不出乎意料地使用了圆角矩形图标，即使是第三方应用也被强制要求使用圆角矩形图标，并且这一规则一直延续到了今天的iOS 11当中，如下图所示：

![](iosicon.jpg)

相反，Android系统在设计的时候就不喜欢苹果这样的封闭与强制，而是选择了自由与开放，对应用图标的形状不做任何强制要求，开发者们可以自由进行选择：

![](androidicon.jpg)

可以看到，在Android上，应用图标可以是方形、圆形、圆角矩形、或者是其他任意不规则图形。

本来就是两家公司不同的设计理念，也说不上孰高孰低。但由于Android操作系统是开源的，国内一些手机厂商在定制操作系统的时候就把这一特性给改了。比如小米手机，就选择了向苹果靠拢，强制要求应用图标圆角化。如果某些应用的图标不是圆角矩形的呢？小米系统会自动给它加上一个圆角的效果，如下图所示：

![](miicon.jpg)

小米的这种做法看上去是向苹果学习，但实际上是相当恶心的。因为谁都可以看出来，这种自动添加的圆角矩形非常丑，因此很多公司就索性直接将应用的图标都设计成圆角矩形的，正好Android和iOS都用同一套图标还省事了。

但是这就让Google不开心了，这不是变向强制要求开发者必须将图标设计成圆角矩形吗？于是在去年的Google I/O大会上，Google点名批评了小米的这种做法，说其违反了Android自由和开放的理念。

除了变向强制要求应用图标圆角化，小米的这种处理方式还有一个弊端，就是如果应用图标的圆角弧度和小米系统要求的不同，那么会出现异常丑陋的效果。

![](miicon2.webp)

该问题也存在了非常之久，Google多年来对此也是睁一只眼闭一只眼。终于在Android 8.0系统中，Google下定决心要好好整治一下Android应用图标的规范性了。

> 原文来自郭林

# 8.0系统的应用图标适配

这个问题对于Google来说还是挺难解决的。因为Google一直在强调自由与开放，那么小米强制要求所有应用图标都必须圆角化也是人家的自由呀，你不准人家这么干是不是本身就违背了自由和开放的理念呢？当然我们在这里讨论这个，有点像讨论先有鸡还是先有蛋的感觉，不过Google还是想出了一套完美的解决方案。

从Android 8.0系统开始，应用程序的图标被分为了两层：前景层和背景层。也就是说，我们在设计应用图标的时候，需要将前景和背景部分分离，前景用来展示应用图标的Logo，背景用来衬托应用图标的Logo。需要注意的是，背景层在设计的时候只允许定义颜色和纹理，但是不能定义形状。

那么应用图标的形状由谁来定义呢？Google将这个权利就交给手机厂商了。不是有些手机厂商喜欢学习苹果的圆角图标吗？没问题，由于应用图标的设计分为了两层，手机厂商只需要在这两层之上再盖上一层mask，这个mask可以是圆角矩形、圆形或者是方形等等，视具体手机厂商而定，就可以瞬间让手机上的所有应用图标都变成相同的规范。原理示意图如下：

![](oicon.gif)

可以看到，这里背景层是一张蓝色的网格图，前景层是一张Android机器人Logo图，然后盖上一层圆形的mask，最终就裁剪出了一张圆形的应用图标。

官方命名为Adaptive Icons。

> 如果你的APP中的targetSdkVersion是低于26的，那么就可以不用进行应用图标适配，Android 8.0系统仍然是向下兼容的。但是如果你将targetSdkVersion指定到了26或者更高，那么Android系统就会认为你的APP已经做好了8.0系统的适配工作，当然包括了应用图标的适配。(图标会有点丑)

# 适配方案

Android Studio 3.0以上的版本中已经内置了8.0系统应用图标适配的功能。

新建一个项目，并查看app的gradle是否已经将targetSdkVersion设置为26。

在查看一下AndroidManifest.xml文件

``` xml
<application
        android:icon="@mipmap/ic_launcher"
        android:label="IconDemo"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:theme="@style/AppTheme">
  //.....
</application>
```

我们需要关注的点是android:icon这个属性，通过这个属性，我们将应用的图标指定为了mipmap目录下的ic_launcher文件。

> 还有一个android:roundIcon属性，这是一个只适用在Android 7.1系统上的过渡版本，很快就被8.0系统的应用图标适配所替代了，我们不用去管它。

![](shipei1.png)

在和以前版本不一样的地方就在于多了一个`mipmap-anydpi-v26`的目录，该目录会使得Android 8.0或以上系统的手机，都会使用这个目录下的ic_launcher来作为图标。

mipmap-anydpi-v26目录下的ic_launcher并不是一张图片，而是一个XML文件，我们打开这个文件看一下，代码如下所示：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<adaptive-icon xmlns:android="http://schemas.android.com/apk/res/android">
    <background android:drawable="@drawable/ic_launcher_background" />
    <foreground android:drawable="@drawable/ic_launcher_foreground" />
</adaptive-icon>
```

这是一个8.0系统应用图标适配的标准写法，在`<adaptive-icon>`标签中定义一个`<background>`标签用于指定图标的背景层，定义一个`<foreground>`标签用于指定图标的前景层。

再打开ic_launcher_background和ic_launcher_foreground后，发现他们只是一个使用SVG格式绘制出来的带纹理的底图。

> SVG格式的图片都是使用AI、PS等图像编辑软件制作之后导出的，基本没有人可以手工编写SVG图片。

一般项目中也可以使用PNG、JPG等格式的图片，或者是一个背景色也可以。

![](runprev.png)

这就是一个前景层盖在背景层上，然后再被圆形mask进行裁剪之后的效果。

# 开始适配

这里的话拿推特的图标作为例子，案例图在下方：

![](tuite.png)

![](tuite_forge.png)

背景色用PS提取出来的颜色值是#1da2f2。

图标已经准备好了，我们开始做图标适配了，回到项目中，然后按下Windows：Ctrl+Shift+A / Mac：command+shft+A 快捷键，并输入Image Asset，如下所示：

![](imageassets.png)

当然也可以对着app(module)目录下右键新建

![](imageassetsother.png)

点击回车键打开Asset Studio编辑器，在这里就可以进行应用图标适配了。

![](adapter1.png)

这个Asset Studio编辑器非常简单好用，一学就会。左边是操作区域，右边是预览区域。

先来看操作区域，第一行的`Icon Type`中选择`Adaptive and Legacy`，表示同时创建兼容8.0系统以及老版本系统的应用图标。第二行的Name用于指定应用图标的名称，这里也保持默认即可。接下来的三个页签，`Foreground Layer`用于编辑前景层，`Background Layer`用于编辑背景层，`Legacy`用于编辑老版本系统的图标。

再来看预览区域，这个就十分简单了，用于预览应用图标的最终效果。在预览区域中给出了可能生成的图标形状，包括圆形、圆角矩形、方形等等。

**注意每个预览图标中都有一个圆圈，这个圆圈叫作安全区域，必须要保证图标的前景层完全处于安全区域当中才行，否则可能会出现图标被手机厂商的mask裁剪掉的情况。**

放一张GIF操作过程：(PS:图比较大，不太懂的压缩)

![](demo.gif)

Android Studio会自动帮我们生成适配8.0系统的应用图标，以及适配老版本系统的应用图标。

来看看运行效果：

![](demo2.png)

这里适配就完成啦。

# 注意事项

Asset Studio自动生成的ic_launcher图标和ic_launcher_round分辨率比较低，在vivo厂商上架应用过程中被提示出将其替换为高清图标，按照vivo提示替换即可。

# 参数说明

```txt
Layer Name - 图层名称
Resize - 制定大小
Round Icon - 仅针对android 7.1 icon处理
Google Play Store Icon - 在google play商店中展示图标
Icon Type - Launcher Icons(Legacy only)
Asset Type - 资源类型，可选图片，剪切画，文本
Path - 资源路径
Name - 如果您不想使用默认名称，可以键入一个新名称。如果资源名称已在项目中存在（向导底部出现错误提示），它将被覆盖。名称只能包含小写字符、下划线和数字。
Trim - 要调整源资产中图标图形与边框之间的边距，请选择 Yes。此操作将移除透明空间，同时保留纵横比。要保持源资产不变，请选择 No。默认值为：No
Padding - 如果您想要调整全部四侧的源资产内边距，请移动滑块。选择 -10% 和 50% 之间的值。如果您也选择了 Trim，则首先会进行剪裁。默认值为：0%
Foreground - 要更改 Clip Art 或 Text 图标的前景色，请点击字段。在 Select Color 对话框中，指定颜色，然后点击 Choose。字段中会显示新值。默认值为：000000
Background - 要更改背景色，请点击字段。在 Select Color 对话框中，指定颜色，然后点击 Choose。字段中会显示新值。默认值为：FFFFFF
Scaling - 要适合图标大小，请选择 Crop 或 Shrink to Fit。选择裁剪，图像边缘会被剪切；选择缩减，图像边缘不会被剪切。源资产仍然不合适时，如果需要，您可以调整内边距。默认值为：Shrink to Fit
Shape - 要为您的源资产添加背景，请选择形状，选项包括圆、正方形、竖直矩形或水平矩形。要想使用透明的背景，请选择 None。默认值为：Square
Effect - 如果您想要为正方形或矩形的右上角添加折角效果，请选择 DogEar。如果不需要，请选择 None。默认值为：None
```

https://developer.android.com/studio/write/image-asset-studio?hl=zh-cn

# 参考资料

https://medium.com/google-design/understanding-android-adaptive-icons-cee8a9de93e2

https://mp.weixin.qq.com/s/WxgHJ1stBjokPi6lTUd1Mg

https://developer.android.com/guide/practices/ui_guidelines/icon_design_adaptive