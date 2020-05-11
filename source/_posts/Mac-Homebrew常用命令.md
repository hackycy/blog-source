---
title: Mac Homebrew常用命令
date: 2019-05-04 12:02:34
tags:
    - Homebrew
categories:
    - 程序人生
---

Homebrew是Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件， 使用命令，非常方便。

<!-- more -->

# 常用命令

``` bash
$ brew --help        #简洁命令帮助
$ man brew           #完整命令帮助
$ brew install git   #安装软件包(这里是示例安装的Git版本控制)
$ brew uninstall git #卸载软件包
$ brew search git    #搜索软件包
$ brew list          #显示已经安装的所有软件包
$ brew update        #同步远程最新更新情况，对本机已经安装并有更新的软件用*标明
$ brew outdated      #查看已安装的哪些软件包需要更新
$ brew upgrade git   #更新单个软件包
$ brew info git      #查看软件包信息
$ brew home git      #访问软件包官方站
$ brew cleanup       #清理所有已安装软件包的历史老版本
$ brew cleanup git   #清理单个已安装软件包的历史版本
$ brew tap           #可以为brew的软件的 跟踪,更新,安装添加更多的的tap formulae 
```

# 程序安装路径及文件夹

- bin          #用于存放所安装程序的启动链接（相当于快捷方式）
- Cellar       #所有brew安装的程序，都将以[程序名/版本号]存放于本目录下
- etc          #brew安装程序的配置文件默认存放路径
- Library      #Homebrew 系统自身文件夹
    + Formula     #程序的下载路径和编译参数及安装路径等配置文件存放地
    + Homebrew    #brew程序自身命令集

# 替换及重置Homebrew默认源

- 替换brew.git:

``` bash
$ cd "$(brew --repo)"
$ git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
```

​	**可选镜像源：**

``` url
https://git.coding.net/homebrew/homebrew.git - Coding
https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git - 清华
https://mirrors.ustc.edu.cn/brew.git - 中科大
```

- 替换homebrew-core.git:

``` bash
$ cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
$ git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
```

- 重置brew.git:

``` bash
$ cd "$(brew --repo)"
$ git remote set-url origin https://github.com/Homebrew/brew.git
```

- 重置homebrew-core.git:

``` bash
$ cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
$ git remote set-url origin https://github.com/Homebrew/homebrew-core.git
```

# LaunchRocket

LaunchRecket是管理homebrew所安装应用的一个管理器，它在系统设置中。

``` bash
# install
brew cask install launchrocket
# uninstall
brew cask uninstall launchrocket
```

https://github.com/jimbojsb/launchrocket

# 参考链接

https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/