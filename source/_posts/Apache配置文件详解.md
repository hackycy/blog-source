---
title: Apache配置文件详解
date: 2019-04-29 20:39:51
tags:
    - Apache
    - 服务器运维
categories:
    - Apache
---

# 环境

- Centos7
- Apache/2.4.38

<!-- more -->

# 配置文件路径

找到apache所在的配置文件，在本文环境下即centos7以yum源安装的httpd所在的配置环境路径为`/etc/httpd/conf`。也可以使用命令找到该配置文件。

``` bash
$ sudo find / -name 'httpd.conf'
```

>以下以本文中的配置路径为准，其他系统下路径可能不一致。
>在 Ubnutu/Mac 上，apache 服务叫 apache2，而不是 httpd（在 Centos 上叫 httpd），主配置文件为 /etc/apache2/apache2.conf

# Apache配置文件

Apache提供了灵活的web服务配置，理解其参数的含义很重要。

Apache配置文件中英文对照：http://www.cnblogs.com/adamite/p/apache_configuration.html

## Apache主目录

`ServerRoot "/etc/httpd"`

## 监听端口

`Listen 80`

## 加载动态模块

`LoadModule php5_module modules/libphp5.so`

或者加载动态模块的配置文件

`Include conf.modules.d/*.conf`

## Apache的进程执行者

``` conf
User apache
Group apache
```

## 服务器域名

该项可配置也可不配置 `ServerName www.example.com:80`

## 网站根目录

`DocumentRoot "/var/www/html"`

## 设置网站根目录的访问权限

``` conf
<Directory "/var/www/html">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.4/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride None

    #
    # Controls who can get stuff from this server.
    #
    Require all granted
</Directory>
```

**参数详解**

- `Allow from all` 参数允许所有人访问`/var/www/html`下的资源
- `Deny from all` 参数拒绝所有人访问`/var/www/html`下的资源
- `Options Indexes` 参数:访问目录时,如果不存在默认首页则展示站点列表 该行建议改成 Options None
- `Options FollowSymLinks` 参数:是否允许快捷方式(ln -s 软连接)
- `Options MultiViews` 多视图,访问`/index`等同访问`index.php`或`index.html`

### Apache服务器访问权限控制包括:

#### Apache 服务权限

``` conf
deny from all (**403 forbidden** error!)
allow from all
```

#### Linux 系统权限

``` conf
selinux
iptables
httpd进程执行者对根目录的权限(**403 forbidden** error!)
```

## 设置目录默认首页

优先级从左往右依次降低

`DirectoryIndex index.html index.php`

## 错误日志

`ErrorLog "logs/error_log"`

## 访问日志

`CustomLog "logs/access_log" common`

## 解析php脚本

`AddType application/x-httpd-php .php`

## 控制错误页面的输出

``` conf
#ErrorDocument 500 "The server made a boo boo."
#ErrorDocument 404 /missing.html
#ErrorDocument 404 "/cgi-bin/missing_handler.pl"
#ErrorDocument 402 http://www.example.com/subscription_info.html
```

## 包含外部配置文件

`Include extra/httpd-vhosts.conf`

## 虚拟目录

`http://localhost/mnt` mnt目录并不在网站根目录下,目录资源在`/tmp/mnt`目录下 在`/usr/local/apache2/etc/http.conf`文件里增加

``` conf
Alias /mnt "/mnt/www" # 虚拟目录（目录别名）
<Directory "/mnt/www">
    Options none
    AllowOverride None
    Order allow,deny
    Deny from all # 拒绝所有
    Allow from all # 允许所有
</Directory>
```

`http://localhost/mnt` 重启apache后访问的资源便是 `/mnt/www`目录下的资源

# Apache虚拟主机配置

基于域名的虚拟主机，指定服务器IP（和可能的端口）使主机接受请求。用`NameVirtualHost`进行配置。 如果服务器上所有的IP地址都会用到，可以用`*`作为`NameVirtualHost`的参数。在`NameVirtualHost`指令中指明IP地址不会使服务器自动侦听那个IP地址。

## 找到Apache的主配置文件`httpd.conf`

``` conf
$ cd /etc/httpd/conf
$ vim httpd.conf
```

我们搜索关键字`vhosts`，如果没有则在文件后添加

``` conf
# Load vhost-config files in the "/etc/httpd/vhost-conf.d" directory if any
Include vhost.d/*.conf
```

>不使用官方原版的单个配置文件有个好处是 每个虚拟主机配置独立开来 减少操作的误差

然后我们到`/etc/httpd/`目录下创建`vhost.d`文件夹

``` bash
$ mkdir vhost.d
$ cd vhost.d
$ pwd
/etc/httpd/vhost.d
$ vim www_sweetlover_cn_net.conf
```

**添加以下内容**

``` conf
<VirtualHost *:80>
    ServerAdmin zjyzy@www.sweetlover.net.cn
    DocumentRoot /var/www/html/sweetlover
    ServerName www.sweetlover.net.cn
    RewriteEngine On
    Options All
    <Directory "/var/www/html/sweetlover">
        # Options -Indexes FollowSymLinks 
        # 为了服务器的安全 Indexes参数一般要取消
        Options FollowSymLinks 
        AllowOverride All
        Allow from all
    </Directory>

    ErrorLog logs/www_sweetlover_net_cn-error_log
    CustomLog logs/www_sweetlover_net_cn-access_log common
</VirtualHost>
```

**再次修改主配置文件`httpd.conf`,找到`Listen 80`，添加以下代码**

``` conf
Listen 80
NameVirtualHost *:80
```

**我们再配置一个同域名下不同端口虚拟主机，配置`8080`端口下的**

``` bash
$ cd /etc/httpd/vhost.d
$ vim www_sweetlover_net_cn_8080.conf
```

针对`www_sweetlover_cn_net.conf`改变相应的配置，即修改`ServerName`为`www.sweetlover.net.cn:8080`，`DocumentRoot`和`Directory`修改为`/var/www/html/sweetlover@8080`。

**修改主配置文件，添加**

``` conf
Listen 8080
NameVirtualHost *:8080
```

>由于环境下为虚拟机环境，直接访问ip加端口号即可访问到配置的网站。在上述配置的DocumentRoot下创建index.html或者index.php即可访问网页。

**如果出现该错误**

``` bash
[warn] default VirtualHost overlap on port 80, the first has precedence
```

>打开apache主配置文件在任意位置添加一行,在 40行的Listion:80后添加一行内容如下:`NameVirtualHost *:80`

>如果使用源码编译安装的，直接去掉注释即可，然后再修改配置文件

``` conf
#LoadModule vhost_alias_module libexec/apache2/mod_vhost_alias.so
```

``` conf
# Virtual hosts
#Include /private/etc/apache2/extra/httpd-vhosts.conf
```

去掉前面`#`注释即可。再修改`/private/etc/apache2/extra/httpd-vhosts.conf`配置即可。

# Apache常见错误

## ServerName配置未填写或错误

``` bash
httpd: Could not reliably determine the server's fully qualified domain name, using luo.centos6.5 for ServerName
```

在Apache主配置文件`httpd.conf`的98行左右`#ServerName www.example.com:80`前面的`#`去掉，换成自己的域名或者ip地址。
例如：修改为`ServerName localhost:80`或者`ServerName 127.0.0.1:80`

## 403 Forbidden错误

``` txt
403 - Forbidden（禁止访问）,服务器拒绝请求
    - forbidden request (matches a deny filter) => HTTP 403
    - The request was a legal request, but the server is refusing to respond to it.
```

**原因1：apache的配置文件没有对站点目录许可**

apache配置文件中没有对站点目录的权限许可配置，这通常是在初始化安装apahce后，更改了默认的apache站点目录导致。

解决办法可能是：通过给主配置文件增加`<Directory "/var/www/html"></Directory>`标签实现对指定目录的权限控制
典型如下(对/var/www目录下的文件允许访问)：

``` conf
<Directory "/var/www">
    Options -Indexes FollowSymLinks # 为了服务器的安全 Indexes参数一般要取消
    AllowOverride None
    Order allow,deny # 允许未被明确拒绝的
    Allow from all
</Directory>
```

**原因2：站点目录下没有首页文件，而apache 的配置又禁止了目录的浏览**

站点目录下没有首页文件(index.php、index.html等默认文件)，而apache的配置又禁止了目录浏览（#Indexes参数:访问目录时,如果不存在默认首页则展示站点列表，该行建议改成`Options None`，也会提示403错误。
解决办法：在站点目录添加默认首页文件或者将配置文件中`Options Index`增加上。

**原因3：deny from all 禁用了所有来访者访问**

``` conf
<Directory "/var/www">
    Options -Indexes FollowSymLinks # 为了服务器的安全 Indexes参数一般要取消
    AllowOverride None
    Order allow,deny # 允许未被明确拒绝的
    Deny from all
    # Deny from 
</Directory>
```

**解决办法：**参考原因2解决方法配置`<Directory ></Directory>`参数。

**站点目录权限问题**

站点目录需要apache的用户有访问权限，否则就会报403错误(一般web站点目录权限给755，站点文件权限给644，上传程序通过另外的上传服务器提供文件上传)。

# Apache服务器优化

## 错误页面优雅显示

可以将404 500等的错误信息页面重定向到网站首页或其他页面，提升用户体验。

编辑apache主配置文件
``` bash
$ vim httpd.conf
```

修改如下内容`ErrorDocument 404 http://www.domain.com`

## `mod_defalte`文件压缩功能

gzip是把文件先在服务器端进行压缩然后再传输，传输完毕后浏览器会重新对压缩过得内容进行解压缩。这样可以显著减少文件传输的大小，没有特殊情况，所有的文本内容都应该被gzip压缩（html,css,js,xml,txt..）

添加如下内容到`httpd.conf`或者`vhost.conf`中

``` conf
<ifmodule mod_deflate.c>
    DeflateCompressionLevel 9
    SetOutputFilter DEFLATE
    AddOutputFilterByType DEFLATE text/html text/plain text/xml
    AddOutputFilterByType DEFLATE application/javscript
    AddOutputFilterByType DEFLATE text/css
</ifmodule>
```

## 更改apache的默认用户

创建apache用户，用于子进程和线程

``` bash
$ useradd -M -s /sbin/nologin webadmin
```

编辑apache的主配置文件,添加或者修改如下内容

``` conf
User webadmin
Group webadmin
```

## 开启apache防盗链功能

主配置文件中增加如下配置

``` conf
<IfModule rewrite_module>
RewriteEngine On
RewriteCond %{HTTP_REFERER} !^http://domain.com/.*$ [NC]
RewriteCond %{HTTP_REFERER} !^http://www.domain.com/.*$ [NC]

RewriteRule .*\.(gif|jpg|swf)$ http://www.domain.com [R,NC]
# RewriteRule .*\.(gif|jpg|swf)$ http://www.domain.com/about/no.png [R,NC]
</IfModule>
```

## 禁止目录Index

``` conf
<Directory "/var/www/html">
    Options -Indexes FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory>
```

或者

``` conf
<Directory "/var/www/html">
    Options FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory>
```

## 禁止用户覆盖(重载)配置文件

``` conf
<Directory "/var/www/html">
    Options FollowSymLinks
    AllowOverride None  # 禁止用户覆盖(重载)配置文件, All即为开启
    Order allow,deny
    Allow from all
</Directory>
```

## 关闭CGI(Common Gateway Interface 通用网关接口)

``` conf
<IfModule alias_module>
ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
</IfModule>
<Directory "/var/www/cgi-bin">
    AllowOverride None
    Options None
    Order allow,deny
    Allow from all
</Directory>
```

## 避免使用.htaccess文件(分布式配置文件)

**默认选项：**`AccessFileName .htaccess` 改为 `# AccessFileName .htaccess`

先考虑性能，如果AllowOverride启用了.haccess文件，则apache需要在每个目录中查找.htaccess文件，因此无论是否真正用到启用.htaccess文件都会导致服务器性能的下降。
　　另外对于每一个请求，都需要读取一次.htaccess文件。
　　其次是安全考虑，这样会允许用户自己修改服务器的配置，这可能会导致某些意想不到的修改，所以请认真考虑是否应道给予用户这样的特权。

>PHP开启路由重写下需要使用

## apache 的安全模块

`mod_evasive20`( 防DDOS攻击)
`mod_limittipconn`(针对单站点)配置
`mod_security`(防止SQL注入)

## apache日志授予root 700权限

**不需要在日志目录给apache用户读或者写权限许可，因为apache的初始进程用户为root**

## 禁止PHP解析指点站点目录

``` conf
Directory "/var/www/html/bbs/Uploads">
    Options FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from all
    php_flag engine off # 注意这行
</Directory>
```

# 参考资料

https://www.kancloud.cn/curder/apache/91272

https://www.linuxidc.com/Linux/2017-05/143590.htm
