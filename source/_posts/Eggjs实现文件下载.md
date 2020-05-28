---
title: Eggjs实现文件下载
date: 2020-05-28 11:47:55
tags:
    - Nodejs
    - Egg
categories:
    - Nodejs
---

# 前言

在最近的编写的一个个人的工具站点中想实现一个网站提交数据后把特定文件压缩打包后浏览器出现下载的功能。找的资料实现也残缺不全。所以记录一下目前能实现的方案。

<!-- more -->

使用到的框架：[EggJS](https://stuk.github.io/jszip/)、[JSZip](https://eggjs.org/zh-cn/)。

# 编写路由

``` javascript
'use strict';

/**
 * @param {Egg.Application} app - egg application
 */
module.exports = app => {
  const { router, controller } = app;
  router.get('/', controller.home.index);
  router.post('/download', controller.home.deal);
};
```

# 编写控制器

``` javascript
'use strict';

const path = require('path');
const fs = require('fs');
const jszip = require('jszip');

const Controller = require('egg').Controller;

class HomeController extends Controller {

  /**
   * 首页
   */
  async index() {
    const { ctx } = this;
    ctx.response.type = 'html';
    ctx.body = fs.readFileSync(path.resolve(__dirname, '../view/home.html'));
  }

  /**
   * 处理数据
   */
  async deal() {
    const { ctx } = this;
    // 处理public Path
    const dirPath = path.resolve(__dirname, `../public`);
    if (!fs.existsSync(dirPath)) {
      fs.mkdirSync(dirPath);
    }
    // 打包
    const zip = new jszip();
    zip.file('test.txt', 'detail');
    zip.folder('img');
    const content = await zip.generateAsync({ type: 'nodebuffer' });
    const zipPath = `${dirPath}/download.zip`;
    fs.writeFileSync(zipPath, content);
    // 返回
    const stats = fs.statSync(zipPath, {
      encoding: 'utf8',
    });
    ctx.response.set({
      'Content-Type': 'application/octet-stream',
      'Content-Disposition': `attachment; filename=download.zip`,
      'Content-Length': stats.size,
    });
    ctx.body = fs.createReadStream(zipPath);
  }

}

module.exports = HomeController;
```

> 首页直接使用文件流来渲染一个网页。不使用nunjucks等模板渲染方式。
>
> deal方法中才是处理下载文件的重点。
>
> 跳转到/download路径时会自动下载download.zip的压缩包。解压后会有一个test.txt文件和一个img的目录。

# 参考资料

https://github.com/eggjs/egg/issues/948