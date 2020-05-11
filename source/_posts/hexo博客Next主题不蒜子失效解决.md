---
title: hexo博客Next主题不蒜子失效解决
date: 2019-03-17 22:54:32
keywords:
    - Hexo
    - Next
    - 不蒜子
    - 博客
tags:
    - hexo
categories:
    - hexo
---

*2018年底发现不蒜子开始失效，但是没有管，现在看了[官网](http://busuanzi.ibruce.info/)*

<!-- more -->

# 原因

官网解释：因七牛强制过期『dn-lbstatics.qbox.me』域名，与客服沟通无果，只能更换域名到『busuanzi.ibruce.info』！

# 解决方案

直接根据提示替换即可

替换路径`themes/next/layout/_third-party/analytics/busuanzi-counter.swig`，打开文件

找到
`<script async src="https://dn-lbstatics.qbox.me/busuanzi/2.3/busuanzi.pure.mini.js"></script>`
替换
`<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>`
