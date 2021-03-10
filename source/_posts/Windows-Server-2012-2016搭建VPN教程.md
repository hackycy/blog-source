---
title: Windows Server 2012/2016搭建VPN教程
date: 2021-03-09 10:17:53
categories:
    - 程序人生
keywords:
    - 小技巧
    - 工具
    - 科学上网
---

# 前置准备

- Windows Server 2012/2016版服务器

> 该文以腾讯云创建的Server 2016服务器为例

<!-- more -->

需要先放通服务器安全组端口，以腾讯云服务器为例：

![](server_port.png)

以及关闭服务器的防火墙。

# 配置步骤

连接远程服务器，打开服务器管理器。

![](open_manager.png)

点击`添加角色和功能`，进行下图所示配置。

![](add_role.png)

开始之前直接点击下一步

![](config_1.png)

安装类型直接点击下一步

![](config_2.png)

服务器选择直接点击下一步

![](config_3.png)

服务器角色增加`网络策略和访问服务`以及`远程访问`

![](config_4.png)

选择添加功能

![](config_5.png)

勾选后点击下一步

![](config_6.png)

功能默认即可，直接点击下一步

![](config_7.png)

网络策略和访问服务直接点击下一步

![](config_8.png)

远程访问直接点击下一步

![](config_9.png)

角色服务增加`DirectAccess和VPN(RAS)`以及路由

![](config_10.png)

选择添加功能即可

![](config_11.png)

勾选后点击下一步

![](config_12.png)

Web服务器角色直接点击下一步

![](config_13.png)

角色服务默认即可直接点击下一步

![](config_14.png)

确认安装

![](config_15.png)

点击安装后等待安装完毕关闭即可。

![](config_16.png)

打开服务器管理器，点击工具，选择路由和远程访问

![](config_17.png)

右键本地服务器，选择“配置并启用路由和远程访问”，启动配置向导。

![](config_18.png)

点击下一步

![](config_19.png)

选择自定义配置

![](config_20.png)

全部勾选，并点击下一步

![](config_21.png)

点击完成

![](config_22.png)

如出现该提示直接点击确定

![](config_23.png)

点击启动服务

![](config_24.png)

展开本地服务器，展开`IPv4`，右键`NAT`，选择`新建接口`

![](config_25.png)

选择`以太网`，点击确定

![](config_26.png)

勾选公用以及启用NAT，如图所示，点击确定

![](config_27.png)

再次展开本地服务器，展开`IPv4`，右键`NAT`，选择`新建接口`

选择`内部`，点击确定

![](config_28.png)

勾选专用，点击确定

![](config_29.png)

配置本地服务器属性

![](config_30.png)

点击`IPv4`标签页为远端连接分配IP地址池

![](config_31.png)

![](config_32.png)

再次配置本地服务器属性

选择`安全`标签页，配置允许L2TP策略选项，并填入**预共享密钥**，点击确定

> 务必记住该密钥，后续需要填写

![](config_33.png)

点击确定

![](config_34.png)

打开服务器管理器，点击工具选择计算机管理

![](config_35.png)

展开本地用户和组，选择新建组

![](config_36.png)

展开本地用户和组，选择新用户

![](config_37.png)

点击新建的用户名右键选择属性

![](config_38.png)

按图中依次点击`隶属于`–`添加`–`高级`—`立即查找`—`VPNGroup（刚刚创建的组）`—`确定`

![](config_39.png)

点击确定

![](config_40.png)

配置 VPN 访问权限，回到服务器管理器，点击`NAPS`–`选择服务器`–启动`网络策略服务器`配置

![](config_41.png)

展开策略，右键点击网络策略并新建

![](config_42.png)

填写名称并在“网络访问服务器的类型”中选择`远程访问服务器(VPN 拨号)`，点击下一步

![](config_43.png)

在指定条件中，根据实际需求，选择合适的匹配条件。比如，文中选择了域中的`VPNGroup`用户组。

![](config_44.png)

![](config_45.png)

![](config_46.png)

点击下一步

![](config_47.png)

点击下一步

![](config_48.png)

点击下一步

![](config_49.png)

点击下一步

![](config_50.png)

点击完成

![](config_51.png)

然后重启服务器（重启服务）但懒得找服务直接重启服务器了。

重启完成后打开服务器管理器，找到远程访问，并启动对应的服务。

![](config_52.png)

右键启动即可

![](config_53.png)

# 连接测试

## Mac环境

打开`设置`—`网络`，点击`+`号新建

![](test_1.png)

选择图中对应类型

![](test_2.png)

![](test_3.png)

![](test_4.png)

## Win10环境

打开`设置`—`网络和Internet`—`VPN`

![](wtest_1.png)

配置如图所示

![](wtest_2.png)

![](wtest_3.png)

# 错误修复

这边测试在Win7或Win10连接时会出现`无法建立计算机与VPN服务器之间的网络连接,因为远程服务器未响应`的问题。查找了文章修复了问题。

## 错误1：因为没有修改过注册表，所以是报这样的错误

![](fix_1.jpg)

**解决办法**

按`windows图标键 + R键` >在运行中输入`regedit`，单击`确定`，进入`注册表编辑器`

![](fix_2.png)

在注册表编辑器”页面的左侧导航树点开 `HKEY_LOCAL_MACHINE`>`SYSTEM`>`CurrentControlSet`>`Services`>`PolicyAgent`

![](fix_3.jpg)

在右边空白处新建 > `DWORD值`，名称为`AssumeUDPEncapsulationContextOnSendRule`

![](fix_4.jpg)

右键单击`AssumeUDPEncapsulationContextOnSendRule`，选择“修改”，进入修改界面，修改值为`2`(表示可以与位于NAT设备后方的服务器建立安全关联)

![](fix_5.jpg)

> 重启电脑即可。一般Win7配置完该步骤后即可连接，但Win10还会出现问题。

## 错误2：修改完注册表，错误就变了，是因为认证的协议问题

![](fix_6.jpg)

**解决办法**

打开更改适配器选项，找到对应的VPN名称的适配器，右键属性

![](fix_7.jpg)

打开安全选项，选择使用这些协议勾上；注意此处还有高级设置里面的L2TP身份验证类型，这里也要填写的（秘钥方式还是证书方式）

![](fix_8.jpg)

> 再次连接，即可修复

# 参考教程

https://www.365jz.com/article/24912

https://me.jinchuang.org/archives/381.html