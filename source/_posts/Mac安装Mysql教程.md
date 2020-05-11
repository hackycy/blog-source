---
title: Mac安装Mysql教程
date: 2019-02-25 13:14:11
tags:
    - Mysql
    - Mac
    - 环境搭建
categories:
    - DBA
---

# 环境

- MacOS10以上

<!-- more -->

# 下载Mysql

这里以Mysql5.7为例

[官网](https://dev.mysql.com/downloads/mysql/5.7.html#downloads)
为了方便开发，推荐直接选择dmg文件下载安装

![](download.png)

# 安装Mysql

直接双击即可，傻瓜式操作。

> 注意，安装成功后会弹出一个对话框，你直接忽略了可以看通知面板，
> 这个就是Mysql的初始密码
> 如图

![](mysqlpwd.png)

# 启动Mysql

安装完毕后系统偏好设置会多出一个选项

![](pane.png)

点击进去

![](mysqlpane.png)

安装之后，默认MySQL的状态是stopped，关闭的，需要点击“Start MySQL Server”按钮来启动它，启动之后，状态会变成running。下方还有一个复选框按钮，可以设置是否在系统启动的时候自动启动MySQL，默认是勾选的，建议取消，节省开机时间。

# 终端连接Mysql

``` bash
vim ~/.bash_profile
```
添加`export PATH=${PATH}:/usr/local/mysql/bin`即可

登陆Mysql
``` bash
mysql -uroot -p
```

输入刚开始安装对话框中提示的初始密码，登陆成功后请修改密码，否则无法执行其他命令。

``` bash
ALTER USER 'root'@'localhost' IDENTIFIED BY 'newpassword';
```



