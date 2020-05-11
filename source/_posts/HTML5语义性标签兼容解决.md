---
title: HTML5语义性标签兼容解决
date: 2019-03-02 14:57:40
keywords:
    - HTML5
    - 兼容
tags:
    - HTML
categories:
    - HTML
---

# 前言
现在大部分的浏览器都能够很好的支持H5，但还是不免得有一些浏览器是不能够支持HTML5的新特性及其一些新标签，特别是在IE8以下的浏览器。

<!-- more -->

# 兼容办法

## 第一种

通过`document.createElement("nav");`的办法来逐个创建标签元素，但是通过其办法还需要定义好其元素的CSS样式。

## 第二种

使用js插件，这里使用的是`html5shiv`，BootCDN有提供,[链接](https://www.bootcdn.cn/html5shiv/)

``` html
<script src="https://cdn.bootcss.com/html5shiv/r29/html5.js"></script>
```

## 终极解决办法

单纯使用第二种的时候，有一些浏览器本身就能够支持，所以在代码中进行判断是否需要该js
使用条件Hack

``` html
<!-- 终极解决方案 -->
    <!--[if lte IE 8]>
        <script src="https://cdn.bootcss.com/html5shiv/r29/html5.js"></script>
    <![endif]-->
```

是否需要兼容可以看业务是否需要，也可以提示用户进行更换浏览器，毕竟IE是个坑爹的浏览器。
