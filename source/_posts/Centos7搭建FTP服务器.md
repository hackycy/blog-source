---
title: Centos7搭建FTP服务器
date: 2018-09-11 23:54:34
tags:
    - Centos7
    - FTP
    - 服务器运维
categories:
    - FTP
keywords:
    - FTP
---

之前部署阿里云服务器需要上传一些文件，所以搭建了FTP服务器，便记录一下。

# 环境
- Centos7

<!-- more -->

# 安装VSFTPD

``` bash
$ yum install vsftpd -y
```

# 配置VSFTPD

vsftpd的配置文件是 __ /etc/vsftpd/vsftpd.conf __ ，直接用vim打开编辑即可。
使用vim编辑器打开vsftpd配置文件：
``` bash
$ vim /etc/vsftpd/vsftpd.conf
```

** 关键修改 **
- anonymous_enable=YES
    是否允许匿名用户登陆FTP。
    为了安全起见关闭这个功能（将等号后的YES改成NO即可）。
- dirmessage_enable=YES
    切换目录时，显示目录下.message文件中的内容
    默认是开启的
- local_umask=022
    FTP上本地的文件权限，默认是077，不过vsftpd安装后的配置文件里默认是022.
    没有什么特殊情况不用修改。
- xferlog_enable=YES
    启用上传和下载的日志功能，默认开启。
- ftpd_banner=XXXX
    FTP的欢迎信息。
- data_connection_timeout=120
    数据连接超时时间。

# 启动VSFTPD

开机启动
``` bash
$ systemctl enable vsftpd.service
$ systemctl start vsftpd.service
```

# 创建FTP用户
修改完vsftpd的配置文件之后我们还是不能使用vsftpd，因为我们还没有设置ftp的用户。
添加一个名为ftpuser的用户，用户文件夹位置为：/home/ftpdir，且禁止此用户登陆服务器：
``` bash
$ useradd -d /home/ftpdir -s /sbin/nologin ftpuser
$ passwd ftpuser
```
这时候系统会要求您输入新的密码并且重复一遍。顺便一提在SSH中，密码一般不会回显，所以初学者可能会觉得输进去没反应，其实是已经输进去了。
调整文件夹权限
``` bash
$ chmod 777 /home/ftpdir
$ cd /home
$ ls -l
```
![](chmod_ftpdir.png)

# 调整防火墙
我这里用的是传统的管理方式
在CentOS 7或RHEL 7或Fedora中防火墙由firewalld来管理，需要还原
``` bash
$ systemctl stop firewalld  
$ systemctl mask firewalld  
```
并且安装iptables-services：
``` bash
$ yum install iptables-services -y
```
设置开机启动：
``` bash
$ systemctl enable iptables
$ systemctl start iptables  
```
## 开放端口

** 主动模式 **
使用Vim编辑器打开iptables配置文件：
``` bash
$ vim /etc/sysconfig/iptables
```
添加
``` bash
-A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT
```
重启服务
``` bash
$ systemctlrestart iptables.service
```

** 被动模式(未测试) **

如果ftp处于被动模式下，除了需要修改iptables的配置文件以外，还需要修改vsftpd的配置文件。
首先是修改vsftpd的配置文件：
使用Vim编辑器打开vsftpd配置文件：
``` bash
$ vim /etc/vsftpd/vsftpd.conf
```

现在配置文件中找到 __ “connect_from_port_20=YES” __ 并将它修改为 __ “connect_from_port_20=NO” __ ，关闭掉vsftpd的主动模式。

然后在配置文件的末尾追加：
** 使vsftpd运行在被动模式 **
pasv_enable=YES
** 被动模式最小端口号30000 **
pasv_min_port=30000
** 被动模式最大端口号31000 **
pasv_max_port=31000

保存配置文件并退出。
然后重启vsftpd服务：
``` bash
$ systemctl restart vsftpd.service
```
然后再使用Vim编辑器打开iptables配置文件：
``` bash
$ vim /etc/sysconfig/iptables
```
添加这两句话：（“#”开头的是注释，可以不添加）
``` bash
#开放ftp协议21端口，允许接受来自21端口的新建TCP连接
-A INPUT -p tcp -m state --state NEW -m tcp --dport 21 -j ACCEPT
#开放30000-31000号端口，允许接受来自此端口号段的新建TCP连接 **
-A INPUT -p tcp --dport 30000:31000 -j ACCEPT
```
保存并退出，然后重启iptables服务：
``` bash
$ systemctl restart iptables.service
```
