---
title: IOS9以上中的ATS问题
date: 2019-03-06 13:54:00
keywords:
    - Ios
    - ATS
tags:
    - Ios
categories:
    - Ios
---

WWDC 15 提出的 `ATS (App Transport Security)` 是 Apple 在推进网络通讯安全的一个重要方式。在 iOS 9 和 OS X 10.11 中，默认情况下非 HTTPS 的网络访问是被禁止的。

<!-- more -->

作为参考，这里将有效的 `NSAppTransportSecurity` 字典结构

``` objc
NSAppTransportSecurity : Dictionary {
    NSAllowsArbitraryLoads : Boolean
    NSAllowsArbitraryLoadsForMedia : Boolean
    NSAllowsArbitraryLoadsInWebContent : Boolean
    NSAllowsLocalNetworking : Boolean
    NSExceptionDomains : Dictionary {
        <domain-name-string> : Dictionary {
            NSIncludesSubdomains : Boolean
            NSExceptionAllowsInsecureHTTPLoads : Boolean
            NSExceptionMinimumTLSVersion : String
            NSExceptionRequiresForwardSecrecy : Boolean   // Default value is YES
            NSRequiresCertificateTransparency : Boolean
        }
    }
}
```

ATS设定的参照表格

![](ats.png)

如果网站内没有启用https，如何关闭，在`info.plist`文件中加入
source code下
``` xml
<key>NSAppTransportSecurity</key>
<dict>
        <key>NSAllowsArbitraryLoads</key>
        <true/>
</dict>    
```
plist下
添加`NSAppTransportSecurity`字典->并添加键值对`NSAllowsArbitraryLoads`=`YES`来禁用ATS

# 参照链接

https://onevcat.com/2016/06/ios-10-ats/

https://stackoverflow.com/questions/31254725/transport-security-has-blocked-a-cleartext-http


