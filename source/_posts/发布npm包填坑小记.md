---
title: 发布npm包填坑小记
date: 2020-09-21 11:09:24
tags:
    - NPM
categories:
    - NodeJS
---

**发布NPM包时遇到的一些问题记录**

# 问题1

```bash
npm ERR! publish Failed PUT 403
npm ERR! code E403
npm ERR! you must verify your email before publishing a new package: https://www.npmjs.com/email-edit : your-package
```

这是注册的npm账号邮箱未进行验证，先去验证。一开始出现这个原因我是邮箱填错一直没收到邮件。

<!-- more -->

# 问题2

在发布`npm`包的时候可能会出现报错信息：

``` bash
npm ERR! 403 403 Forbidden - PUT https://registry.npm.taobao.org/@hackycy%2fegg-typeorm - [no_perms] Private mode enable, only admin can publish this module
npm ERR! 403 In most cases, you or one of your dependencies are requesting
npm ERR! 403 a package version that is forbidden by your security policy.
```

出现这个问题是因为当前设置的是`cnpm`登录到的是`cnpm`，所以需要切换回来。
之前登录的时候就提出登录的是`taobao`只不过那个时候没注意。

可以输入一下命令查看当前的登录源：

```bash
$ npm config get registry
https://registry.npm.taobao.org/
```

可以看到返回的地址是淘宝源，需要切回到npmjs源，输入以下命令：

``` bash
$ npm config set registry=http://registry.npmjs.org
```

设置完之后在查看当前登录的源地址：

``` bash
$ npm config get registry
http://registry.npmjs.org/
```

然后重新`npm login`再发布即可。

# 问题3

```bash
npm ERR! publish Failed PUT 403
npm ERR! code E403
npm ERR! You do not have permission to publish "your-package". Are you logged in as the correct user? : your-package
```

你的包和别人的包重名了，npm 里的包不允许重名，所以去 [npm](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.npmjs.com%2F) 搜一下，改个没人用的名字就可以了。或者用`@your-name/your-package`来命名。

# 问题4

无法发布私有包：

``` bash
npm ERR! publish Failed PUT 402
npm ERR! code E402
npm ERR! You must sign up for private packages :
```

大多数是因为当你的包名为`@your-name/your-package`时才会出现，原因是当包名以`@your-name`开头时，`npm publish`会默认发布为私有包，但是 npm 的私有包需要付费，所以需要添加如下参数进行发布：

``` bash
npm publish --access public
```

# 参考资料

https://blog.csdn.net/weixin_38080573/article/details/88080556