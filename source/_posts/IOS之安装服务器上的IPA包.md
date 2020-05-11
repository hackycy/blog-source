---
title: IOS之安装服务器上的IPA包
date: 2019-09-11 12:20:59
keywords:
    - Ios
    - IPA
tags:
    - Ios
categories:
    - Ios
---

# 前言

如果想要将导出的ad-hoc包或者企业级别开发者账号才能够打包的in-house的ipa包通过自己服务器上下载安装的话，还需要一些简单的配置。

<!-- more -->

# 部署准备

准备文件：一个plist文件，ipa安装包，网页下载页面（可不需要），`57*57`像素icon与`512*512`像素icon。

这里利用github来进行测试打包发布应用。github仓库测试完就会删除，需要自行进行准备。

由于本人没有企业级别的开发者账号，只能通过打包ad-hoc包来进行测试。ad-hoc包如果profile签名文件中没有安装在已有的测试设备上是无法安装使用的。而企业级别的in-house包只需要信任即可。

> plist文件在IOS7之后仅支持部署在https。

## plist文件

一个plist模版：

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>items</key>
	<array>
		<dict>
			<key>assets</key>
			<array>
				<dict>
					<key>kind</key>
					<string>software-package</string>
					<key>url</key>
					<string>ipa安装包路径</string>
				</dict>
				<dict>
					<key>kind</key>
					<string>display-image</string>
					<key>url</key>
					<string>57*57像素icon路径</string>
				</dict>
				<dict>
					<key>kind</key>
					<string>full-size-image</string>
					<key>url</key>
					<string>512*512像素icon路径</string>
				</dict>
			</array>
			<key>metadata</key>
			<dict>
				<key>bundle-identifier</key>
				<string>cn.sweetlover.jytools</string>
				<key>bundle-version</key>
				<string>1.2</string>
				<key>kind</key>
				<string>software</string>
				<key>title</key>
				<string>jytools</string>
			</dict>
		</dict>
	</array>
</dict>
</plist>
```

或者在打包的时候一并使用xcode生成

![](plist.png)

next后，填写完即可。

![](plist2.png)

导出后会看到一个manifest.plist文件就是所需要用到的文件。

> 在生成的时候可以随便填写一些https的域名即可，但是真正发布时候还是需要修改好配置。

# 部署

**可以先上传ipa包和icon文件上传到github，获取到下载路径之后填写到plist文件当中，然后再将plist文件上传到github。**

Demo完整plist文件配置

``` xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
	<key>items</key>
	<array>
		<dict>
			<key>assets</key>
			<array>
				<dict>
					<key>kind</key>
					<string>software-package</string>
					<key>url</key>
					<string>https://github.com/hackycy/mitaoquan/raw/master/jytools.ipa</string>
				</dict>
				<dict>
					<key>kind</key>
					<string>display-image</string>
					<key>url</key>
					<string>https://github.com/hackycy/mitaoquan/raw/master/icon-57.png</string>
				</dict>
				<dict>
					<key>kind</key>
					<string>full-size-image</string>
					<key>url</key>
					<string>https://github.com/hackycy/mitaoquan/raw/master/icon-512.png</string>
				</dict>
			</array>
			<key>metadata</key>
			<dict>
				<key>bundle-identifier</key>
				<string>net.cn.sweetlover.mitaoquan</string>
				<key>bundle-version</key>
				<string>1.5</string>
				<key>kind</key>
				<string>software</string>
				<key>title</key>
				<string>mitao</string>
			</dict>
		</dict>
	</array>
</dict>
</plist>
```

github目录：

![](public.png)

获取plist文件的路径，通过

![](public2.png)

案例中获取的plist文件路径为`https://raw.githubusercontent.com/hackycy/mitaoquan/master/manifest.plist`

最后通过`itms-services://?action=download-manifest&url=`后面拼接上plist的url即可。

**demo中为`itms-services://?action=download-manifest&url=https://raw.githubusercontent.com/hackycy/mitaoquan/master/manifest.plist`**

> 前端可以直接通过location.href或者a标签等跳转该链接即可安装。本地测试可以直接用safari打开上面的地址即可安装

这里直接本地测试，复制该链接到safari直接打开安装。

![](public3.png)

![](public4.png)

# 原理

通过`itms-services`协议，在safari浏览器可以直接在ios设备上安装应用程序。`itms-services`协议需要一个plist配置文件。`itms-services://?action=download-manifest&url=`是固定不变的，url根据环境变化。