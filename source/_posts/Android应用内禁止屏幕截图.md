---
title: Android应用内禁止屏幕截图
date: 2019-07-09 10:18:59
keywords:
    - Android
tags:
    - Android
categories:
    - Android
---

一个晚上在看了一下微信的收付款功能，想截个图，突然发现提示了一句**由于受安全政策限制，无法截取屏幕截图**，再研究了一下手机的其他APP，中国建设银行APP上也有该提示，出于好奇，查阅了一下该功能的实现方式。

<!-- more -->

该功能的实现方式很简单，就是在`setContentView`前加上一句代码

``` java
getWindow().setFlags(WindowManager.LayoutParams.FLAG_SECURE, WindowManager.LayoutParams.FLAG_SECURE);
```

![](demo.jpeg)

Google官方对于[FLAG_SECURE](https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_SECURE)的解释：

>Display flag: Indicates that the display has a secure video output and supports compositing secure surfaces.
>
>If this flag is set then the display device has a secure video output and is capable of showing secure surfaces. It may also be capable of showing `protected buffers`.
>
>If this flag is not set then the display device may not have a secure video output; the user may see a blank region on the screen instead of the contents of secure surfaces or protected buffers.
>
>Secure surfaces are used to prevent content rendered into those surfaces by applications from appearing in screenshots or from being viewed on non-secure displays. Protected buffers are used by secure video decoders for a similar purpose.
>
>An application creates a window with a secure surface by specifying the `WindowManager.LayoutParams#FLAG_SECURE`window flag. Likewise, an application creates a `SurfaceView` with a secure surface by calling `SurfaceView#setSecure` before attaching the secure view to its containing window.
>
>An application can use the absence of this flag as a hint that it should not create secure surfaces or protected buffers on this display because the content may not be visible. For example, if the flag is not set then the application may choose not to show content on this display, show an informative error message, select an alternate content stream or adopt a different strategy for decoding content that does not rely on secure surfaces or protected buffers.
>
>**See also:**
>
>- `getFlags()`
>
>Constant Value: 2 (0x00000002)

在设置了该FLAG后，发现该FLAG还可以实现以下功能：

- 阻止屏幕截图
- 在任务切换界面中（Recent apps）只显示应用名字和图标，不会显示APP具体内容。

**参考资料**

https://developer.android.com/reference/android/view/WindowManager.LayoutParams.html#FLAG_SECURE

https://developer.android.com/reference/android/view/Display.html#FLAG_SECURE