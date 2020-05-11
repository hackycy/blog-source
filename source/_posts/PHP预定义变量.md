---
title: PHP预定义变量
date: 2019-03-19 17:03:31
tags:
    - PHP
categories:
    - PHP
---

# PHP预定义变量

对于全部脚本而言，PHP 提供了大量的预定义变量。这些变量将所有的外部变量表示成内建环境变量，并且将错误信息表示成返回头。

<!-- more -->

超全局变量 — 超全局变量是在全部作用域中始终可用的内置变量

- `$GLOBALS` — 引用全局作用域中可用的全部变量
- `$_SERVER` — 服务器和执行环境信息
- `$_GET` — HTTP GET 变量
- `$_POST` — HTTP POST 变量
- `$_FILES` — HTTP 文件上传变量
- `$_REQUEST` — HTTP Request 变量
- `$_SESSION` — Session 变量
- `$_ENV` — 环境变量
- `$_COOKIE` — HTTP Cookies
- `$php_errormsg` — 前一个错误信息
- `$HTTP_RAW_POST_DATA` — 原生POST数据
- `$http_response_header` — HTTP 响应头
- `$argc` — 传递给脚本的参数数目
- `$argv` — 传递给脚本的参数数组

参考资料

https://secure.php.net/manual/zh/reserved.variables.php


