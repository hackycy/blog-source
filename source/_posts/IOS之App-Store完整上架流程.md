---
title: IOS之App Store完整上架流程
date: 2019-08-20 16:59:20
keywords:
    - Ios
    - App Store
tags:
    - Ios
categories:
    - Ios
---

最近在处理App Store上架的问题，和Android的打包流程比起来的确比较棘手些，因为涉及到很多的一些概念，现在也完整的梳理并记录一下。

<!-- more -->

# 前言

发布上架都需要一个苹果开发者账号，免费的账号是有诸多限制的。

**苹果开发者帐号体系**

**Apple Developer：**直接在[Apple Developer](https://link.juejin.im/?target=https%3A%2F%2Fdeveloper.apple.com%2F)登录，同意Apple Developer协议后的账号，免费，只可以使用Xcode进行真机调试，Xcode 7之后苹果推出的功能

**Apple Developer Program：**分个人和组织类型，费用都是每年 99 美元，可以使用Xcode进行真机调试，打包Ad-Hoc测试，在App Store发布App

**Apple Developer Enterprise Program：**企业账号，费用是每年 299 美元，可以使用Xcode进行真机调试，打包Ad-Hoc测试，打包In-House App，但不能在App Store发布App

不同安装方式对应的证书类型

- 非App Store
  - Development（真机调试）：iOS App Development
  - Ad Hoc：iOS Distribution (App Store and Ad Hoc)
  - Enterprise：iOS Distribution (In-House and Ad Hoc)
- App Store：iOS Distribution (App Store and Ad Hoc)

在iOS的项目中，只要不是运行在模拟器上，都会涉及到开发者帐号、证书、`Provisioning Profile`这些概念。

**免费账号的限制：**

- 创建的`Provisioning Profile`有效期只有7天
- 在7天内最多注册10个Bundle Id
- 只能同时注册3台iOS设备
- 在同一台iOS设备上，只能同时安装3个使用免费账号签名的App。当该设备上已经存在3个App，则无法安装任何免费账号签名的任何App，就算是那3个App其中一个也不行，只能先把其中一个删除

> 以下介绍是以我的Apple Developer Program个人账号来进行演示

# 概念

证书、加密等原理这里不作介绍，主要讲解一些IOS所必要的概念。

## .certSigningRequest

在`Mac`中的`钥匙串访问`里的 `证书助理 -> 从证书颁发机构请求证书`：

![](certSigningRequest.png)

最后会创建出一个`.certSigningRequest`文件，其实这个过程就是创建了一对公私钥

![](certSigningRequest2.png)

- 其中`.certSigningRequest`文件保存着
  - 申请者信息申请者的公钥
  - 摘要算法
  - 公钥加密算法
- 私钥保存在 `keychain`中

## AppleWWDRCA证书

`iOS`以及 `mac OS`（在安装 Xcode 时）将自动安装 `AppleWWDRCA.cer`这个中间证书（Intermediate Certificates），它实际上就是 iOS（开发）证书的证书，即根证书（Apple Root Certificate）。

## iOS App Development证书

iOS的开发证书，在开发阶段进行真机测试时需要用到的证书。可以在苹果开发网站上手动创建，需要上传`.certSigningRequest`文件；或者使用Xcode自动创建。

## iOS Distribution证书

iOS的发布证书，可以用于进行 `Ad Hoc` 测试、打包上传到 `App Store` 或者打包成 `Enterprisee（In-House）` 类型供企业内部使用。可以在苹果开发网站上手动创建，需要上传`.certSigningRequest`文件；或者使用Xcode自动创建。

## .p12

在`Mac`的`钥匙串访问`里选择一张证书，右击该证书，选择`导出"xxxxx"`，然后设置密码，可以导出该证书对应的`.p12`文件。`.p12`文件包含个人信息、公钥和私钥，也就是`证书 + 私钥`。iOS类型的每种证书同时存在数量有限制，而证书是依靠`mac OS`上的`.certSigningRequest`文件创建的，所以正常情况下，每种类型的证书只能在有限的Mac电脑上使用，如果需要在更多不同的Mac电脑上进行App开发、测试、签名，可以导出对应`.p12`文件代替证书来使用。

![](p12.png)

## Provisioning Profile

`Provisioning Profile`的文件格式为`.mobileprovision`，里面包含着

- 可以使用的证书
- App ID，由 TeamID 和 BundleID 组合而成，类似于 `A1B2C3D4.com.domain.appName`形式
- 可安装该App的设备列表的UDID
- Entitlements，授权文件，列出了App可以进行哪些行为
- 以上信息的签名

在苹果开发网站上手动创建，或者使用Xcode自动创建。

## .ipa

`.ipa`文件是iOS上的App安装文件，其实它只是一个压缩包，等同于`.zip`格式，用`mac OS`自带的`归档实用工具`可以直接对它解压，可以看到里面的内容

![](ipa.png)

## .app

右击`.app`文件，选择`显示包内容`，可以看到里面的内容

![](app.png)

`.app`文件主要包含三部分：

- `Mach-O`格式的二进制可执行文件，这个是一个App最重要的文件，我们编写的`Objective-C`、`Swift`代码都被编译在里面
- 资源文件，包括：`.bundle`文件，`.framework`文件，`.dylib`文件，`.nib`文件，图片文件，音视频文件，字体文件等所有项目用到的文件
- `CodeResources`，签名信息
- `embedded.mobileprovision`文件，或者`entitlements`文件
  - 对于没有上传App Store的`.app`文件，里面会包含`embedded.mobileprovision`文件，没有`entitlements`文件
  - App Store下载的`.app`文件，里面会包含`.entitlements`文件，没有`embedded.mobileprovision`文件

# Automatic signing

在Xcode 7之前，只有加入到Apple Developer Program（即付费）才能进行真机调试，Xcode 7之后苹果推出了`Automatic signing`功能，只要在Xcode上登陆Apple ID，就会自动管理证书和Provisioning Profile，同时没有加入Apple Developer Program的账号也能进行真机调试。

![](automanagesigning.png)

勾选Xcode中的`AutoMatically manager signing`，选择对应的`Team`后，无论是加入Apple Developer Program的账号（即付费账号）还是Apple Developer的账号（即免费账号）：

- 如果Xcode没有帮该账号自动生成过`iOS App Development`类型的证书， 无论在苹果后台是否已经存在其他`iOS App Development`类型的证书，都会生成一张新的`iOS App Development`类型证书，证书名称的格式是：开发者账号名称(当前Mac电脑名称)， 如：Brian Hui (Daniels的MacBook Pro)，同时会保存在当前Mac电脑的`keychain`中
- 免费账号无法进入苹果的管理证书后台，但可以猜测出在苹果后台也会存在该证书
- 如果Xcode没有帮该App的Bundle ID自动生成过对应的`Provisioning Profile`，就会使用上面那张证书生成一个`Provisioning Profile`，保存在`~/Library/MobileDevice/Provisioning Profiles`，但在苹果后台则不会存在这个`Provisioning Profile`
- 如果在`钥匙串访问`中删除了那张证书，Xcode会提示你的账号有`iOS App Development`类型的证书，但这台电脑没有安装，需要先把那张证书`Revoke`，`Revoke`后会再次重复前面的步骤，生成新的证书和`Provisioning Profile`
- 如果在`~/Library/MobileDevice/Provisioning Profiles`里面，删除了该`Provisioning Profile`文件，Xcode会马上重新生成`Provisioning Profile`

在使用Xcode的`Automatic signing`功能的前提下，进行`Archive`，然后`Distribute App`的时候，选择非`Development`的选项，再选择AutoMatically manager signing

- 如果本地存在`iOS Distribution`类型的证书，则会直接进行重签名
- 如果没有存在`iOS Distribution`类型的证书，而苹果的后台有，则会告诉你，该账号存在`iOS Distribution`类型的证书，但这台电脑没有安装，请联系创建人拿到备份（.p12文件）进行安装，当你安装了该证书（或者.p12文件），则会直接进行重签名
- 如果没有存在`iOS Distribution`类型的证书，而苹果的后台也没有，则Xcode会询问你是否需要生成`iOS Distribution`类型的证书，如果选择需要，则会自动生成`iOS Distribution`类型的证书，并且建议你保存在本地，证书名称的格式是：Team Name， 如：Hutchison Telephone (Macau) Company Limited，同时使用这张证书生成一个`Provisioning Profile`，保存在`~/Library/MobileDevice/Provisioning Profiles`，但在苹果后台则不会存在这个`Provisioning Profile。

## Xcode对Provisioning Profile的验证

Xcode怎么把App和证书、`Provisioning Profile`绑定在一起呢？什么时候需要一张新的证书，什么时候需要一个新的`Provisioning Profile`？

Bundle ID是App的唯一标识，App和证书、`Provisioning Profile`绑定在一起，其实就是Bundle ID和证书、`Provisioning Profile`绑定在一起，两种情况：

### Automatic signing

Bundle ID与开发者账号绑定。使用`Automatic signing`时，选择开发者账号（Team）后，Xcode会根据开发者账号去本地检索是否存在该账号对应的`Provisioning Profile`，再验证是否存在与该Bundle ID匹配的`Provisioning Profile`，再根据`Provisioning Profile`去本地检索是否存在对应的证书，都验证通过，则会设置成功。如果不存在`Provisioning Profile`，则会判断该Bundle ID是否已经被其他账号注册，如果已经被其他账号注册，则整个流程失败，需要选择对应的账号。如果该Bundle ID没有被其他账号注册或者账号已经对应上，则按照文章前面所说的步骤，最后生成`Provisioning Profile`。

### 没有使用Automatic signing

需要手动选择`Provisioning Profile`，当选择了其中一个`Provisioning Profile`时，则会分别验证Bundle ID是否对应、`Provisioning Profile`是否过期、是否存在对应的证书、App的权限是否对应、证书的类型和`Code Signing Identity`设置是否对应，如果都通过验证，则会设置成功。

# App Store上架打包流程

xcode构建好一个bundle id为`cn.sweetlover.uploadstore`的项目，假设已为一个正常的项目APP。

> 空demo的APP是无法构建成功的。

准备好一个开发者账号，并登陆[苹果开发者官网](https://developer.apple.com/)，选择`Certificates, IDs & Profiles`：

![](appstore2.png)

## 创建APP ID

选择`Identifiers`，创建一个APP ID：

![](appstore5.png)

这里创建一个BundleID为`cn.sweetlover.uploadstore`，BundleID格式这里不细讲了。

![](appstore3.png)

下面的选择是想要让你APP所接入的能力，例如常用的推送功能

![](appstore4.png)

选择继续，并注册后，会看到`Identifiers`会出现你刚刚创建的APP ID：

![](appstore6.png)

## 注册测试设备

选择`Devices`，添加

![](appstore7.png)

填写设备名称和UDID即可，UDID可以使用ITunes查看。

![](appstore8.png)

## 创建CSR

打开`Mac`->`钥匙串访问`->`证书助理`->`从证书颁发机构请求证书..`，填写，并选择存储到磁盘

![](appstore10.png)

![](appstore11.png)

## 创建证书

选择`Certificates`，添加

### 创建开发证书

选择`IOS App Development`，并继续

![](appstore9.png)

上传CSR文件，即选择我们刚刚创建好的CSR文件。并继续

![](appstore12.png)

创建好后，选择下载证书，双击安装：

![](appstore13.png)

打开钥匙串访问即可看到我们刚刚安装好的证书

![](appstore14.png)

发布证书就创建完毕了。

### 创建发布证书

重复上述创建开发证书的步骤，只不过选项发生变化：

![](appstore15.png)

最后下载安装，看到钥匙串访问出现证书即可

![](appstore16.png)

## 创建Profiles

选择`Profiles`

### 创建开发Provisioning Profile

选择`IOS App Development`，并继续

![](appstore17.png)

选择好我们之前创建的APP ID，如`cn.sweetlover.uploadstore`，并继续

![](appstore18.png)

选择开发证书，并继续

![](appstore19.png)

选择测试设备，并继续

![](appstore20.png)

填写Provisioning Profile Name，并选择生成

![](appstore21.png)

下载并双击安装：

![](appstore22.png)

![](appstore24.png)

xcode签名方式选择手动以便查看：

![](appstore23.png)

### 创建发布Provisioning Profile

重复上述创建开发证书的步骤，只不过选项发生变化：

![](appstore25.png)

xcode：

![](appstore26.png)

### 注意事项

如果不安装证书，选择profile，xcode会报错缺少证书文件：

![](appstore27.png)

## App Store Connect发布

### APP创建

点击`Account`登陆后点击`App Store Connect`，选择`我的App`

![](appstore1.png)

新建APP，填写好资料，并创建，创建后进入APP，编辑APP应用信息和定价情况

![](appstore28.png)

![](appstore30.png)

### 版本提交

首次提交会出现以下界面，填写好里面所需要的资料，里面都可按照提示进行编写。

![](appstore32.png)

如果已经发布了，需要继续发版本，则点击

![](appstore33.png)

由于首次提交还未上架过，所以按钮是置灰的状态，无法点击。

## Xcode Archive构建版本

进入`Xcode`选择`Product`->`Archive`，注意不要选中到模拟器，要选中`Generic IOS Device`。因为上架的包不是针对某种设备来进行Archive的。

![](appstore34.png)

出现以下界面，选择Distrubite App，如果Archive过多次版本也会出现以往的版本。

![](appstore35.png)

选择IOS App Store

![](appstore36.png)

这里直接选择上传，也可以选择导出成IPA，使用Application Loader进行发布。

![](appstore37.png)

next~

![](appstore38.png)

选择好发布的证书和profile文件

![](appstore39.png)

选择上传即可。

![](appstore40.png)

经过一段漫长的时间等待后，回到App Store Connect的页面中，可能需要一段时间等到，建置版本后出现按钮即可选择构建好的版本。

![](appstore41.png)

填写好所需要的资料就可以提交审核了。

# 关于Application Loader

打开`Xcode` -> `Open Developer Tool` -> `Application Loader`，登陆你的APP ID。

![](applicationloader.png)

密码登陆非APPLE ID的密码，需要登录appleid.apple.com上，输入AppleID密码后会产生一个密码，去`Application Loader`中输入产生的密码即可。

![](applicationloader2.png)

# 关于TestFlight

等上传构建版本处理完成后，一般会显示缺少出口合规证明。点击黄色提示那，在弹出的页面选择否，点击开始内部测试。

![](testflight.png)

选择App Store connect用户选项，点击测试员旁边+号，选择测试的苹果账号！

![](testflight2.png)

然后手机端下载TestFlight后，兑换邀请码即可。

# App审核指南

App Store Review Guidelines：https://developer.apple.com/app-store/review/guidelines/