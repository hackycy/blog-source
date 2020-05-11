---
title: Android P/9.0 http网络请求异常问题
date: 2019-03-06 00:18:43
keywords:
    - Android
    - Android P
    - OKhttp
tags:
    - Android
categories:
    - Android
---

*使用Android P系统时，app使用http请求出现异常。*

<!-- more -->

# 异常原因

>Google表示，为保证用户数据和设备的安全，针对下一代 Android 系统(Android P) 
>的应用程序，将要求默认使用加密连接，这意味着 Android P 将禁止 App 
>使用所有未加密的连接，因此运行 Android P 
>系统的安卓设备无论是接收或者发送流量，未来都不能明码传输，需要使用下一代(Transport Layer 
>Security)传输层安全协议，而 Android Nougat 和 Oreo 则不受影响。

因此在`Android P`中使用`HttpUrlConnection`时会报出次异常

``` java
W/System.err: java.io.IOException: Cleartext HTTP traffic to **** not permitted
```

而使用OKHttp时，同样也会报出异常

``` java
java.net.UnknownServiceException: CLEARTEXT communication ** not permitted by network security policy 
```

在Android P系统的设备上，如果应用使用的是非加密的明文流量的http网络请求，则会导致该应用无法进行网络请求，https则不会受影响，同样地，如果应用嵌套了webview，webview也只能使用https请求。
有人认为 Android P 上所有的 App 都需要使用 TLS 加密会降低上网体验，事实上这是一种误解，至于 App 对于少数旧服务器的连接如果非要使用明码传输，开发者需要更改 App 的网络安全配置以允许此类连接。

# 解决方案

- APP内所有请求更改为Https

- 将targetSdkVersion改为27以下

- 开启/允许http请求

  **方法一**

  在`res`下新增一个`xml`目录，然后创建一个名为：`network_security_config.xml` 文件（名字自定） ，内容如下，大概意思就是允许开启http请求

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  
  <network-security-config>
  
   <base-config cleartextTrafficPermitted="true" />
  
  </network-security-config>
  ```

  然后在`AndroidManifest.xml`文件中的Application标签下增加一行属性

  ```xml
  <application
          ...
          android:networkSecurityConfig="@xml/network_security_config"
          ...>
  
      ...
  
  </application>
  ```

  **方法二**

  在清单文件中加入 **android:usesCleartextTraffic="true"** 属性

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <manifest ...>
      <uses-permission android:name="android.permission.INTERNET" />
      <application
          
          android:usesCleartextTraffic="true"
          >
          
      </application>
  </manifest>
  ```

  更多参数修改：https://developer.android.com/training/articles/security-config#trust-anchors

# 参考链接

https://www.jianshu.com/p/57047a84e559

https://android-developers.googleblog.com/2018/04/protecting-users-with-tls-by-default-in.html 

https://android-developers.googleblog.com/2018/04/dns-over-tls-support-in-android-p.html
