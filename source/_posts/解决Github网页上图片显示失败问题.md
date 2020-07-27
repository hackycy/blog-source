---
title: 解决Github网页上图片显示失败问题
date: 2020-05-29 16:04:10
tags:
    - github
categories:
    - 程序人生
---

# 问题

最近科学上网失效的有些频繁，上Github的时候图片总是加载不出来，之前没有在意，但是自己博客中的图片也是依赖Github的时候就留意到了这个问题。**（如果有科学上网请随意）**

<!-- more -->

![](https://github.static.si-yee.com/posts/SolvetheproblemofimagedisplayfailureonGitHubwebpage/20200604140631.png)

主要报错是`Failed to load resource: net::ERR_CERT_COMMON_NAME_INVALID`以及`Failed to load resource: net::ERR_CONNECTION_RESET`

**来自网友的解释：**

实际上，可以认为，`ERR_CERT_COMMON_NAME_INVALID`就是用一个错误的域名访问了某个节点的`https`资源。导致这个错误的原因，基本是：

1. dns污染
2. host设置错误
3. 官方更新了dns，但是dns缓存没有被更新，导致错误解析。

# 解决方法

## 方案一

打开`github`任意未显示图片的网页，使用元素选择器（`Ctrl+Shift+C`）放在显示不了的图片上，或者在无法显示的图片上右键-检查元素，定位到该图片的标签，那么你得到了它的URL，叫做`src`属性。

![](https://github.static.si-yee.com/posts/SolvetheproblemofimagedisplayfailureonGitHubwebpage/20200604140727.png)

将URL复制出来后，打开[IPAddress.com](https://www.ipaddress.com/)这个网站，搜索它的域名:`avatars2.githubusercontent.com`

![](https://github.static.si-yee.com/posts/SolvetheproblemofimagedisplayfailureonGitHubwebpage/20200604140757.png)

搜索后会看到该域名的信息和`IP`地址：

![](https://github.static.si-yee.com/posts/SolvetheproblemofimagedisplayfailureonGitHubwebpage/20200604140836.png)

当前该域名的IP是：199.232.68.133，那么我们就可以使这个**IP**和**域名**映射起来。

**修改hosts**

Window下路径`C:\Windows\System32\drivers\etc\hosts`

Mac、Linux下路径`/etc/hosts`

``` txt
# GitHub Start 
140.82.113.3      github.com
140.82.114.20     gist.github.com

151.101.184.133    assets-cdn.github.com
151.101.184.133    raw.githubusercontent.com
151.101.184.133    gist.githubusercontent.com
151.101.184.133    cloud.githubusercontent.com
151.101.184.133    camo.githubusercontent.com
151.101.184.133    avatars0.githubusercontent.com
199.232.68.133     avatars0.githubusercontent.com
199.232.28.133     avatars1.githubusercontent.com
151.101.184.133    avatars1.githubusercontent.com
151.101.184.133    avatars2.githubusercontent.com
199.232.28.133     avatars2.githubusercontent.com
151.101.184.133    avatars3.githubusercontent.com
199.232.68.133     avatars3.githubusercontent.com
151.101.184.133    avatars4.githubusercontent.com
199.232.68.133     avatars4.githubusercontent.com
151.101.184.133    avatars5.githubusercontent.com
199.232.68.133     avatars5.githubusercontent.com
151.101.184.133    avatars6.githubusercontent.com
199.232.68.133     avatars6.githubusercontent.com
151.101.184.133    avatars7.githubusercontent.com
199.232.68.133     avatars7.githubusercontent.com
151.101.184.133    avatars8.githubusercontent.com
199.232.68.133     avatars8.githubusercontent.com

# GitHub End
```

保存即可，**该文件随时可能变更，请随时自行更新，可以在该项目中查看最新Hosts：[Github520](https://github.com/521xueweihan/GitHub520)**

> window下无法保存，没有修改权限，鼠标右键-属性-安全-修改权限；或将`hosts`文件复制一份，修改之后，复制到原文件夹替换！

## 方案二

**Window系统下**

还可以使用`ipconfig/flush`对本地DNS缓存进行一次刷新，如果遇到网络异常，可能是DNS缓存的问题，刷新一下，步骤。

windows开始→运行→输入：CMD 按回车键，打开命令提示符窗口。
再输入： `ipconfig /flushdns` 回车，执行命令，可以重建本地DNS缓存。

**Mac系统下**

OS X Mountain Lion or Lion

Use the following Terminal command to reset the DNS cache:

```text
sudo killall -HUP mDNSResponder
```

Mac OS X v10.6

Use the following Terminal command to reset the DNS cache:

```text
sudo dscacheutil -flushcache
```

mac Mojave

搬运from：[https://www.hongkiat.com/blog/how-to-clear-dns-cache-in-macos-mojave/](https://link.zhihu.com/?target=https%3A//www.hongkiat.com/blog/how-to-clear-dns-cache-in-macos-mojave/)

1. 打开终端：launchpad里面的“终端”
2. 输入：sudo killall -HUP mDNSResponder; sleep 2; 然后敲回车；
3. 输入密码；然后敲回车
4. command+Q退出，就好啦

# 参考资料

https://www.zhihu.com/question/19679715

https://www.ipaddress.com/

https://www.jianshu.com/p/30e2703a5647

https://blog.csdn.net/qq_38232598/article/details/91346392

https://juejin.im/post/5f18683af265da22f84d7602?utm_source=gold_browser_extension