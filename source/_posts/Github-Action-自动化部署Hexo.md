---
title: Github Action 自动化部署Hexo
date: 2020-05-26 13:51:33
tags:
    - Github Action
    - hexo
categories:
    - Github Action
---

# 什么是Github Action

`Github Actions`是`Github`推出的一个新的功能，可以为我们的项目自动化地构建工作流，例如代码检查，自动化打包，测试，发布版本等等。入口在项目`pull request`的旁边。

一次无意中查看到阮一峰大神博客中提到的Github Action，平常一般玩过的是Jenkins。看到可以将React项目自动部署到Github Page时，灵感就想到了自己的基于Hexo搭建的博客来实现一下自动部署。查了一下资料，的确可以实现的。

<!-- more -->

> 该文章不涉及Github Action以及Hexo的教学。请自行查阅

# 部署前准备

先准备两个仓库，一个是部署博客所产出静态页面（github page）的仓库，一个是博客所有源文件的仓库

在本地生成一个 key：

``` bash
$ ssh-keygen -t rsa -b 4096 -C "your email" -f ~/.ssh/github-actions-deploy
```

> （也可以生成其他种类的 key，如果用上面的命令，需要修改一下用户名） 回车完成即可

查看私钥和公钥

``` bash
$ cd ~/.ssh
$ cat github-actions-deploy
$ cat github-actions-deploy.pub
```

在博客所有源文件的仓库**Settings -> Secrets** 里添加刚刚生成的私钥，名称为 `ACTION_DEPLOY_KEY`。

然后在 Github Pages 的仓库，**Settings -> Deploy keys** 添加刚刚生成的公钥，名称随意，但要勾选 **Allow write access**。

![image](https://user-images.githubusercontent.com/26972260/82867966-592bb400-9f5e-11ea-8601-22368fbe956a.png)

# 添加Github Action

可以在网页上 **Actions** 里编辑配置文件，也可以直接在本地目录添加直接 commit。

本文直接在本地目录提交配置文件的话，将配置文件存至 `.github/workflows/*随便起名*.yml`。

``` yaml
name: Deploy

on:
  push:
    branches:    
      - master

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
      - name: Setup Node 
        uses: actions/setup-node@v1
        with:
          node-version: '10.x'
      - name: prepare build env
        env:
          ACTION_DEPLOY_KEY: ${{ secrets.ACTION_DEPLOY_KEY }}
        run: |
          # set up private key for deploy
          mkdir -p ~/.ssh/
          echo "$ACTION_DEPLOY_KEY" | tr -d '\r' > ~/.ssh/id_rsa
          chmod 600 ~/.ssh/id_rsa
          ssh-keyscan github.com >> ~/.ssh/known_hosts
          # set git infomation
          git config --global user.name 'hackycy'
          git config --global user.email 'qa894178522@qq.com'
      - name: clone
        run: git clone https://github.com/hackycy/hackycy.github.io.git .deploy_git
      - name: NPM
        run: npm install
      - name: Clean
        run: ./node_modules/.bin/hexo clean
      - name: Generate
        run: ./node_modules/.bin/hexo generate
      - name: Deploy
        run: ./node_modules/.bin/hexo deploy
```

{% note info %}

**对这个配置文件做几点说明：**

1. Actions 在早期测试时用的是 HCL 格式，而现在使用 YAML 配置，HCL 格式配置文件已被废弃。YAML 格式需要严格按照缩进。
2. `on` 标注什么事件会触发这个 workflow，可以指定 branches，详情参考[文档](https://help.github.com/en/articles/events-that-trigger-workflows)。
3. `runs-on` 设置运行平台，目前有 Windows、Ubuntu、macOS，见[文档](https://help.github.com/en/articles/virtual-environments-for-github-actions)。
4. `uses` 是使用打包好的 action，可以通过 `with` 传参数。官方提供了一些 Git 基本操作和环境安装的包，也可以使用 Docker。
5. `env` 可以设置这一步的环境变量，这一步设置的变量不会继承到下一步。刚才设置的私钥可以通过 `$` 获取到，具体见[文档](https://help.github.com/en/articles/virtual-environments-for-github-actions)。另外直接将密钥 echo 出来会被打码 :)
6. Git 配置请更改为自己的。
7. 由于我的博客源仓库配置了Submodule，如果没有配置的请直接删除step name为clone的步骤

{% endnote %}

# 自动构建

每当你写完博客是直接提交到源仓库即可自动触发构建。会配合Action自动发布到Github Page仓库。

![image](https://user-images.githubusercontent.com/26972260/82869130-5336d280-9f60-11ea-8226-f548c61d122d.png)

# 注意事项

如果博客中配置了主题，请注释掉主题下的.gitignore的一些配置，例如本人使用的Next主题

![image](https://user-images.githubusercontent.com/26972260/82867335-406ece80-9f5d-11ea-88fd-64ee69618acc.png)

以及配合主题安装的第三方插件，有一些插件通过Git clone安装时不会上传到源文件仓库，需要手动删除掉插件目录下的.git文件夹。

# 参考资料

http://www.ruanyifeng.com/blog/2019/09/getting-started-with-github-actions.html

[https://xiaopc.org/2019/08/29/github-actions-%E6%B5%8B%E8%AF%95-%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2-hexo/](https://xiaopc.org/2019/08/29/github-actions-测试-自动部署-hexo/)

https://hateonion.me/posts/20feb22/

