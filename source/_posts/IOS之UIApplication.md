---
title: IOS之UIApplication
date: 2019-06-20 11:18:53
keywords:
    - Ios
    - Objective-C
tags:
    - Objective-C
    - Ios
categories:
    - Ios
---

UIApplication 的核心作用是提供了 iOS 程序运行期间的控制和协作工作。

每一个程序在运行期必须有且仅有一个`UIApplication`（或则其子类）的一个实例。在程序开始运行的时候，`UIApplicationMain` 函数是程序进入点，这个函数做了很多工作，其中一个重要的工作就是创建一个 `UIApplication` 的单例实例。在你的代码中你，你可以通过调用 `[UIApplication sharedApplication] `来得到这个单例实例的指针。

<!-- more -->

UIApplication 的一个主要工作是处理用户事件，它会起一个队列，把所有用户事件都放入队列，逐个处理，在处理的时候，它会发送当前事件 到一个合适的处理事件的目标控件。此外，UIApplication 实例还维护一个在本应用中打开的 window 列表（UIWindow 实例），这样它就 可以接触应用中的任何一个 UIView 对象。UIApplication 实例会被赋予一个代理对象，以处理应用程序的生命周期事件（比如程序启动和关闭）、系统事件（比如来电、记事项警告）等等。

# UIApplicaion 生命周期

一个 UIApplication 可以有如下几种状态：

- `Not running（未运行）`程序没启动
- `Inactive（未激活）`程序在前台运行，不过没有接收到事件。在没有事件处理情况下程序通常停留在这个状态
- `Active（激活）`程序在前台运行而且接收到了事件。这也是前台的一个正常的模式
- `Background（后台）` 程序在后台而且能执行代码，大多数程序进入这个状态后会在在这个状态上停留一会。时间到之后会进入挂起状态 (Suspended)。有的程序经过特殊的请求后可以长期处于 Background 状态
- `Suspended（挂起）`程序在后台不能执行代码。系统会自动把程序变成这个状态而且不会发出通知。当挂起时，程序还是停留在内存中的，当系统内存低时，系统就把挂起的程序清除掉，为前台程序提供更多的内存。

常见的代理方法有

1. `(void)applicationWillResignActive:(UIApplication *)application`

   说明：当应用程序将要入非活动状态执行，在此期间，应用程序不接收消息或事件，比如来电话了

2. `(void)applicationDidBecomeActive:(UIApplication *)application`

   说明：当应用程序入活动状态执行，这个刚好跟上面那个方法相反

3. `(void)applicationDidEnterBackground:(UIApplication *)application`

   说明：当程序被推送到后台的时候调用。所以要设置后台继续运行，则在这个函数里面设置即可

4. `(void)applicationWillEnterForeground:(UIApplication *)application`

   说明：当程序从后台将要重新回到前台时候调用，这个刚好跟上面的那个方法相反。

5. `(void)applicationWillTerminate:(UIApplication *)application`

   说明：当程序将要退出是被调用，通常是用来保存数据和一些退出前的清理工作。这个需要设置 UIApplicationExitsOnSuspend 的键值。

6. `(void)applicationDidReceiveMemoryWarning:(UIApplication *)application`

   说明：iPhone 设备只有有限的内存，如果为应用程序分配了太多内存操作系统会终止应用程序的运行，在终止前会执行这个方法，通常可以在这里进行内存清理工作防止程序被终止

7. `(void)applicationSignificantTimeChange:(UIApplication*)application`

   说明：当系统时间发生改变时执行

8. `(void)applicationDidFinishLaunching:(UIApplication*)application`

   说明：当程序载入后执行

# 应用入口

``` objc
- (BOOL)application:(UIApplication *)application willFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    return YES;
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // Override point for customization after application launch.
    return YES;
}
```

说明：这两个方法的作用几乎完全一样，只是执行顺序有先后。AppDelegate类默认出现的是`application: didFinishLaunchingWithOptions:`，一般情况下只需要处理这个方法就可以了。

`didFinishLaunchingWithOptions`函数的参数launchOptions是一个NSDictionary类型的对象，存储的是程序启动的原因。

`didFinishLaunchingWithOptions`函数的返回值是一个BOOL类型，将决定是否处理URL资源，如果返回YES，则会由`application:openURL:sourceApplication:annotation:`方法处理URL。如果应用程序有一个远程通知启动，返回值会被忽略。

> P.s. 如果同时出现了`application: willFinishLaunchingWithOptions:`和`application: didFinishLaunchingWithOptions:`，那么这两个方法都要返回YES，才会处理URL资源。

## 启动原因

-  **用户直接启动**
   lauchOptions内无数据

-  **其他应用程序通过openURL:启动**
   lauchOptions字典内存储:
   @{UIApplicationLaunchOptionsURLKey:启动URL(NSURL),
   UIApplicationLaunchOptionsSourceApplicationKey:启动源程序的bundleID(NSString)}

-  **本地通知启动**
   lauchOptions字典内存储：
   @{UIApplicationLaunchOptionsLocalNotificationKey:启动应用程序的本地通知对象（UILocalNotification）}

- **远程通知启动**
   @{UIApplicationLaunchOptionsRemoteNotificationKey:启动应用程序的的远程通知信息userInfo（NSDictionary）}

- **其他的key还有**
   UIApplicationLaunchOptionsAnnotationKey,
   UIApplicationLaunchOptionsLocationKey,
   UIApplicationLaunchOptionsNewsstandDownloadsKey

  > 程序launchOptions中的可能键值可以参考UIApplication Class Reference的”Launch Options Keys”。

例子：在`didFinishLaunchingWithOptions:`创建UIWindow

> 先把指定的Main Interface中mian删除掉

``` objc
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    //创建窗口对象
    self.window = [[UIWindow alloc]initWithFrame:[UIScreen mainScreen].bounds];
    //创建窗口的根控制器，并且赋值
    UIViewController *rootVc = [[UIViewController alloc]init];
    self.window.rootViewController = rootVc;
    //显示窗口
    [self.window makeKeyAndVisible];
    return YES;
}
```

# App 生命周期示意图

![](lifecycle.jpg)

