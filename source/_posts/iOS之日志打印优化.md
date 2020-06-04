---
title: iOS之日志打印优化
date: 2020-05-21 15:02:35
keywords:
    - Ios
    - Swift
tags:
    - Ios
categories:
    - Ios
---

# 前言

在日常工作当中，Debug模式的打印调试是基操了。而唯一的一招不是`NSLog`就是`print`。但项目开发中使用Swift后`NSLog`已经不常用了。

<!-- more -->

而在日常工作当中使用Swift的`debugPrint`来进行对一些网络请求的输出等的调试已经习以为常。但是Swift编译器并不会将`print`和`debugPrint`删除。在最终发布的App会把内容输出到终端。造成了性能的损失。

但是你也不可能随时都能在上线前把这些打印语句都删去。

# 解决方法

但是更好的方法是通过添加条件编译来将这些语句排除在 Release 版本外。在 Xcode 的 Build Setting 中，在 **Other Swift flags** 的 Debug 栏中加入 `-D DEBUG` 即可加入一个编译标识。

![](https://github.static.si-yee.com/posts/iOS之日志打印优化-20200604133855.png)

之后我们就可以通过将 `print` 或者 `debugPrint` 包装一下：

``` swift
func dPrint(item: Any) {
    #if DEBUG
    print(item)
    #endif
}
```

这样，在 Release 版本中，`dPrint` 将会是一个空方法，所有对这个方法的调用都会被编译器剔除掉。需要注意的是，在这种封装下，如果你传入的 `items` 是一个表达式而不是直接的变量的话，这个表达式还是会被先执行求值的。如果这对性能也产生了可测的影响的话，我们最好用 `@autoclosure` 修饰参数来重新包装 `print`。这可以将求值运行推迟到方法内部，这样在 Release 时这个求值也会被一并去掉：

``` swift
func dPrint(item: @autoclosure () -> Any) {
    #if DEBUG
    print(item())
    #endif
}

dPrint(resultFromHeavyWork())
// Release 版本中 resultFromHeavyWork() 不会被执行
```

> 使用Time Profiler + 真机测试可以验证

# 参考资料

https://onevcat.com/2016/02/swift-performance/